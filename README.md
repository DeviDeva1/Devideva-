# Devideva-
import React, { useState, useEffect } from 'react';
import { Heart, MessageSquare, User, Settings, Search, X } from 'lucide-react';

// Initialize Firebase (mock for now, will be replaced by actual setup if persistence is requested)
// const firebaseConfig = typeof __firebase_config !== 'undefined' ? JSON.parse(__firebase_config) : {};
// const app = firebaseConfig && Object.keys(firebaseConfig).length ? initializeApp(firebaseConfig) : null;
// const db = app ? getFirestore(app) : null;
// const auth = app ? getAuth(app) : null;

function App() {
  // State for navigation between different sections of the app
  const [currentPage, setCurrentPage] = useState('browse'); // 'browse', 'profile', 'matches', 'chat', 'settings'
  // State for authentication readiness and user ID (mocked for now)
  const [userId, setUserId] = useState('mock-user-id');
  const [isAuthReady, setIsAuthReady] = useState(true); // Assume auth is ready for front-end demo

  // Mock user profiles
  const [profiles, setProfiles] = useState([
    {
      id: 'user1',
      name: 'Alice',
      age: 28,
      location: 'New York, NY',
      bio: 'Loves hiking, reading, and exploring new coffee shops. Looking for someone adventurous!',
      interests: ['Hiking', 'Reading', 'Coffee', 'Travel'],
      imageUrl: 'https://placehold.co/400x400/FFD700/000000?text=Alice',
    },
    {
      id: 'user2',
      name: 'Bob',
      age: 32,
      location: 'San Francisco, CA',
      bio: 'Software engineer by day, amateur chef by night. Passionate about tech and good food.',
      interests: ['Coding', 'Cooking', 'Gaming', 'Movies'],
      imageUrl: 'https://placehold.co/400x400/87CEEB/000000?text=Bob',
    },
    {
      id: 'user3',
      name: 'Charlie',
      age: 26,
      location: 'Austin, TX',
      bio: 'Musician and artist. Always looking for inspiration and good conversations.',
      interests: ['Music', 'Art', 'Nature', 'Photography'],
      imageUrl: 'https://placehold.co/400x400/98FB98/000000?text=Charlie',
    },
    {
      id: 'user4',
      name: 'Diana',
      age: 30,
      location: 'Chicago, IL',
      bio: 'Fitness enthusiast and dog lover. Enjoy long walks and trying new restaurants.',
      interests: ['Fitness', 'Dogs', 'Food', 'Outdoors'],
      imageUrl: 'https://placehold.co/400x400/FF6347/000000?text=Diana',
    },
  ]);

  // State for current profile being viewed in browse mode
  const [currentProfileIndex, setCurrentProfileIndex] = useState(0);

  // Mock matches and chats
  const [matches, setMatches] = useState([]);
  const [chats, setChats] = useState({}); // { profileId: [{sender: 'me', message: 'Hi'}, {sender: 'other', message: 'Hello!'}] }

  // Function to handle liking a profile
  const handleLike = (profileId) => {
    console.log(`Liked profile: ${profileId}`);
    // In a real app, this would update Firestore and check for a mutual like
    const newMatches = [...matches];
    if (!newMatches.includes(profileId)) {
      newMatches.push(profileId);
      setMatches(newMatches);
      // Simulate a match immediately for demo purposes
      console.log(`It's a match with ${profiles[currentProfileIndex].name}!`);
      setChats(prevChats => ({
        ...prevChats,
        [profileId]: [{ sender: 'system', message: `You matched with ${profiles[currentProfileIndex].name}!` }]
      }));
    }
    showNextProfile();
  };

  // Function to handle disliking a profile
  const handleDislike = () => {
    console.log('Disliked profile');
    showNextProfile();
  };

  // Function to show the next profile
  const showNextProfile = () => {
    setCurrentProfileIndex((prevIndex) => (prevIndex + 1) % profiles.length);
  };

  // Render content based on current page
  const renderContent = () => {
    switch (currentPage) {
      case 'browse':
        const profile = profiles[currentProfileIndex];
        if (!profile) {
          return (
            <div className="flex flex-col items-center justify-center h-full p-4">
              <p className="text-xl font-semibold text-gray-700">No more profiles for now! Check back later.</p>
              <button
                onClick={() => setCurrentPage('profile')}
                className="mt-6 px-6 py-3 bg-indigo-600 text-white font-semibold rounded-full shadow-lg hover:bg-indigo-700 transition duration-300 transform hover:scale-105"
              >
                Create Your Profile
              </button>
            </div>
          );
        }
        return (
          <div className="flex flex-col items-center justify-center p-4">
            <div className="bg-white rounded-xl shadow-2xl overflow-hidden w-full max-w-sm transform transition-all duration-300 hover:scale-105">
              <img
                src={profile.imageUrl}
                alt={`${profile.name}'s profile`}
                className="w-full h-80 object-cover rounded-t-xl"
                onError={(e) => {
                  e.target.onerror = null;
                  e.target.src = `https://placehold.co/400x400/A0A0A0/FFFFFF?text=${profile.name}`;
                }}
              />
              <div className="p-6">
                <h2 className="text-3xl font-bold text-gray-900 mb-2">{profile.name}, {profile.age}</h2>
                <p className="text-lg text-gray-600 mb-4">{profile.location}</p>
                <p className="text-gray-700 leading-relaxed mb-6">{profile.bio}</p>
                <div className="flex flex-wrap gap-2 mb-6">
                  {profile.interests.map((interest, index) => (
                    <span key={index} className="bg-indigo-100 text-indigo-800 text-sm font-medium px-3 py-1 rounded-full">
                      {interest}
                    </span>
                  ))}
                </div>
                <div className="flex justify-around items-center pt-4 border-t border-gray-200">
                  <button
                    onClick={handleDislike}
                    className="p-4 rounded-full bg-red-100 text-red-600 hover:bg-red-200 transition duration-300 transform hover:scale-110 focus:outline-none focus:ring-2 focus:ring-red-500 focus:ring-opacity-50"
                    title="Dislike"
                  >
                    <X size={32} strokeWidth={2.5} />
                  </button>
                  <button
                    onClick={() => handleLike(profile.id)}
                    className="p-4 rounded-full bg-green-100 text-green-600 hover:bg-green-200 transition duration-300 transform hover:scale-110 focus:outline-none focus:ring-2 focus:ring-green-500 focus:ring-opacity-50"
                    title="Like"
                  >
                    <Heart size={32} strokeWidth={2.5} fill="currentColor" />
                  </button>
                </div>
              </div>
            </div>
          </div>
        );
      case 'profile':
        // Mock user profile for editing
        const myProfile = {
          name: 'You',
          age: 25,
          location: 'Your City, ST',
          bio: 'Tell us about yourself!',
          interests: ['Add', 'Interests'],
          imageUrl: 'https://placehold.co/400x400/94a3b8/FFFFFF?text=Your+Profile',
        };
        return (
          <div className="p-6 bg-white rounded-xl shadow-2xl max-w-lg mx-auto">
            <h2 className="text-3xl font-bold text-gray-900 mb-6 text-center">My Profile</h2>
            <div className="flex flex-col items-center mb-6">
              <img
                src={myProfile.imageUrl}
                alt="Your profile"
                className="w-48 h-48 object-cover rounded-full shadow-lg mb-4"
              />
              <button className="px-4 py-2 bg-indigo-100 text-indigo-700 rounded-full hover:bg-indigo-200 transition duration-300">
                Upload Photo
              </button>
            </div>
            <div className="space-y-4">
              <div>
                <label className="block text-gray-700 text-sm font-bold mb-2" htmlFor="name">Name</label>
                <input
                  type="text" id="name" defaultValue={myProfile.name}
                  className="shadow appearance-none border rounded-lg w-full py-3 px-4 text-gray-700 leading-tight focus:outline-none focus:ring-2 focus:ring-indigo-500"
                />
              </div>
              <div>
                <label className="block text-gray-700 text-sm font-bold mb-2" htmlFor="age">Age</label>
                <input
                  type="number" id="age" defaultValue={myProfile.age}
                  className="shadow appearance-none border rounded-lg w-full py-3 px-4 text-gray-700 leading-tight focus:outline-none focus:ring-2 focus:ring-indigo-500"
                />
              </div>
              <div>
                <label className="block text-gray-700 text-sm font-bold mb-2" htmlFor="location">Location</label>
                <input
                  type="text" id="location" defaultValue={myProfile.location}
                  className="shadow appearance-none border rounded-lg w-full py-3 px-4 text-gray-700 leading-tight focus:outline-none focus:ring-2 focus:ring-indigo-500"
                />
              </div>
              <div>
                <label className="block text-gray-700 text-sm font-bold mb-2" htmlFor="bio">Bio</label>
                <textarea
                  id="bio" defaultValue={myProfile.bio} rows="4"
                  className="shadow appearance-none border rounded-lg w-full py-3 px-4 text-gray-700 leading-tight focus:outline-none focus:ring-2 focus:ring-indigo-500"
                ></textarea>
              </div>
              <div>
                <label className="block text-gray-700 text-sm font-bold mb-2" htmlFor="interests">Interests (comma separated)</label>
                <input
                  type="text" id="interests" defaultValue={myProfile.interests.join(', ')}
                  className="shadow appearance-none border rounded-lg w-full py-3 px-4 text-gray-700 leading-tight focus:outline-none focus:ring-2 focus:ring-indigo-500"
                />
              </div>
            </div>
            <button className="mt-8 w-full bg-indigo-600 text-white font-semibold py-3 rounded-full shadow-lg hover:bg-indigo-700 transition duration-300 transform hover:scale-105">
              Save Profile
            </button>
          </div>
        );
      case 'matches':
        return (
          <div className="p-6 bg-white rounded-xl shadow-2xl max-w-lg mx-auto">
            <h2 className="text-3xl font-bold text-gray-900 mb-6 text-center">Your Matches</h2>
            {matches.length === 0 ? (
              <p className="text-center text-gray-600">You haven't matched with anyone yet. Keep swiping!</p>
            ) : (
              <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
                {matches.map((matchId) => {
                  const matchedProfile = profiles.find(p => p.id === matchId);
                  if (!matchedProfile) return null;
                  return (
                    <div key={matchedProfile.id} className="bg-indigo-50 p-4 rounded-lg shadow-md flex items-center space-x-4">
                      <img
                        src={matchedProfile.imageUrl}
                        alt={matchedProfile.name}
                        className="w-16 h-16 rounded-full object-cover"
                      />
                      <div>
                        <h3 className="text-xl font-semibold text-gray-800">{matchedProfile.name}</h3>
                        <p className="text-gray-600">{matchedProfile.location}</p>
                        <button
                          onClick={() => { setCurrentPage('chat'); /* set active chat */ }}
                          className="mt-2 text-indigo-600 hover:underline text-sm"
                        >
                          Send Message
                        </button>
                      </div>
                    </div>
                  );
                })}
              </div>
            )}
          </div>
        );
      case 'chat':
        return (
          <div className="p-6 bg-white rounded-xl shadow-2xl max-w-lg mx-auto h-96 flex flex-col">
            <h2 className="text-3xl font-bold text-gray-900 mb-6 text-center">Chats</h2>
            {Object.keys(chats).length === 0 ? (
              <p className="text-center text-gray-600 flex-grow flex items-center justify-center">No active chats. Match with someone to start chatting!</p>
            ) : (
              <div className="flex-grow overflow-y-auto border rounded-lg p-3 mb-4 bg-gray-50">
                {Object.entries(chats).map(([profileId, messages]) => {
                  const matchedProfile = profiles.find(p => p.id === profileId);
                  if (!matchedProfile) return null;
                  return (
                    <div key={profileId} className="mb-4">
                      <h4 className="font-semibold text-indigo-700 mb-2">Chat with {matchedProfile.name}</h4>
                      {messages.map((msg, index) => (
                        <div key={index} className={`flex ${msg.sender === 'me' ? 'justify-end' : 'justify-start'} mb-2`}>
                          <div className={`p-3 rounded-lg max-w-[80%] ${msg.sender === 'me' ? 'bg-indigo-500 text-white' : 'bg-gray-200 text-gray-800'}`}>
                            {msg.message}
                          </div>
                        </div>
                      ))}
                      <div className="flex mt-2">
                        <input
                          type="text"
                          placeholder={`Message ${matchedProfile.name}...`}
                          className="flex-grow border rounded-l-lg py-2 px-3 focus:outline-none focus:ring-2 focus:ring-indigo-500"
                        />
                        <button className="bg-indigo-600 text-white py-2 px-4 rounded-r-lg hover:bg-indigo-700 transition duration-300">
                          Send
                        </button>
                      </div>
                    </div>
                  );
                })}
              </div>
            )}
          </div>
        );
      case 'settings':
        return (
          <div className="p-6 bg-white rounded-xl shadow-2xl max-w-lg mx-auto">
            <h2 className="text-3xl font-bold text-gray-900 mb-6 text-center">Settings</h2>
            <div className="space-y-4">
              <div>
                <label className="block text-gray-700 text-sm font-bold mb-2" htmlFor="notification">Email Notifications</label>
                <select
                  id="notification"
                  className="shadow border rounded-lg w-full py-3 px-4 text-gray-700 leading-tight focus:outline-none focus:ring-2 focus:ring-indigo-500"
                >
                  <option>On</option>
                  <option>Off</option>
                </select>
              </div>
              <div>
                <label className="block text-gray-700 text-sm font-bold mb-2" htmlFor="distance">Max Distance (miles)</label>
                <input
                  type="range" id="distance" min="1" max="100" defaultValue="50"
                  className="w-full h-2 bg-gray-200 rounded-lg appearance-none cursor-pointer range-lg"
                />
                <p className="text-sm text-gray-500 mt-1">Currently: 50 miles</p>
              </div>
              <button className="w-full bg-red-600 text-white font-semibold py-3 rounded-full shadow-lg hover:bg-red-700 transition duration-300 transform hover:scale-105">
                Log Out
              </button>
            </div>
          </div>
        );
      default:
        return null;
    }
  };

  return (
    <div className="min-h-screen bg-gradient-to-br from-indigo-500 to-purple-600 font-inter flex flex-col items-center p-4 sm:p-6 md:p-8">
      <style>
        {`
          @import url('https://rsms.me/inter/inter.css');
          :root { font-family: 'Inter', sans-serif; }
          @supports (font-variation-settings: normal) {
            :root { font-family: 'Inter var', sans-serif; }
          }
          /* Custom range slider thumb styling */
          input[type="range"]::-webkit-slider-thumb {
            -webkit-appearance: none;
            width: 24px;
            height: 24px;
            border-radius: 50%;
            background: #4F46E5; /* Indigo-600 */
            cursor: pointer;
            box-shadow: 0 0 0 4px rgba(79, 70, 229, 0.3);
            transition: background 0.15s ease-in-out;
          }
          input[type="range"]::-moz-range-thumb {
            width: 24px;
            height: 24px;
            border-radius: 50%;
            background: #4F46E5; /* Indigo-600 */
            cursor: pointer;
            box-shadow: 0 0 0 4px rgba(79, 70, 229, 0.3);
            transition: background 0.15s ease-in-out;
          }
          input[type="range"]::-webkit-slider-thumb:hover {
            background: #4338CA; /* Indigo-700 */
          }
          input[type="range"]::-moz-range-thumb:hover {
            background: #4338CA; /* Indigo-700 */
          }
        `}
      </style>

      <div className="w-full max-w-2xl bg-gray-50 rounded-3xl shadow-xl flex flex-col overflow-hidden min-h-[70vh] md:min-h-[80vh]">
        {/* Header/Title */}
        <header className="bg-white p-4 sm:p-6 shadow-md text-center">
          <h1 className="text-4xl font-extrabold text-indigo-700 tracking-tight">
            Connect <span className="text-purple-600">&</span> Find
          </h1>
        </header>

        {/* Main Content Area */}
        <main className="flex-grow p-4 sm:p-6 md:p-8 overflow-y-auto">
          {renderContent()}
        </main>

        {/* Navigation Bar */}
        <nav className="bg-white p-4 sm:p-6 border-t border-gray-200 flex justify-around items-center">
          <button
            onClick={() => setCurrentPage('browse')}
            className={`flex flex-col items-center text-sm font-medium p-2 rounded-lg transition-all duration-300 ${currentPage === 'browse' ? 'text-indigo-600 bg-indigo-50 shadow-md transform scale-105' : 'text-gray-500 hover:text-indigo-500'}`}
          >
            <Search size={24} strokeWidth={2} />
            <span className="mt-1 hidden sm:block">Browse</span>
          </button>
          <button
            onClick={() => setCurrentPage('matches')}
            className={`flex flex-col items-center text-sm font-medium p-2 rounded-lg transition-all duration-300 ${currentPage === 'matches' ? 'text-indigo-600 bg-indigo-50 shadow-md transform scale-105' : 'text-gray-500 hover:text-indigo-500'}`}
          >
            <Heart size={24} strokeWidth={2} />
            <span className="mt-1 hidden sm:block">Matches</span>
          </button>
          <button
            onClick={() => setCurrentPage('chat')}
            className={`flex flex-col items-center text-sm font-medium p-2 rounded-lg transition-all duration-300 ${currentPage === 'chat' ? 'text-indigo-600 bg-indigo-50 shadow-md transform scale-105' : 'text-gray-500 hover:text-indigo-500'}`}
          >
            <MessageSquare size={24} strokeWidth={2} />
            <span className="mt-1 hidden sm:block">Chat</span>
          </button>
          <button
            onClick={() => setCurrentPage('profile')}
            className={`flex flex-col items-center text-sm font-medium p-2 rounded-lg transition-all duration-300 ${currentPage === 'profile' ? 'text-indigo-600 bg-indigo-50 shadow-md transform scale-105' : 'text-gray-500 hover:text-indigo-500'}`}
          >
            <User size={24} strokeWidth={2} />
            <span className="mt-1 hidden sm:block">Profile</span>
          </button>
          <button
            onClick={() => setCurrentPage('settings')}
            className={`flex flex-col items-center text-sm font-medium p-2 rounded-lg transition-all duration-300 ${currentPage === 'settings' ? 'text-indigo-600 bg-indigo-50 shadow-md transform scale-105' : 'text-gray-500 hover:text-indigo-500'}`}
          >
            <Settings size={24} strokeWidth={2} />
            <span className="mt-1 hidden sm:block">Settings</span>
          </button>
        </nav>
      </div>
    </div>
  );
}

export default App;
