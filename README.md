# Fitness-pwa-tracker
// fitness-pwa-tracker/public/manifest.json
{
  "short_name": "FitnessPWA",
  "name": "Fitness Tracker PWA",
  "icons": [
    {
      "src": "favicon.ico",
      "sizes": "64x64 32x32 24x24 16x16",
      "type": "image/x-icon"
    }
  ],
  "start_url": ".",
  "display": "standalone",
  "theme_color": "#000000",
  "background_color": "#ffffff"
}

// fitness-pwa-tracker/public/service-worker.js
const CACHE_NAME = 'fitness-pwa-cache-v1';
const urlsToCache = [
  '/',
  '/index.html',
  '/manifest.json',
  '/favicon.ico'
];

self.addEventListener('install', (event) => {
  event.waitUntil(
    caches.open(CACHE_NAME)
      .then(cache => cache.addAll(urlsToCache))
  );
});

self.addEventListener('fetch', (event) => {
  event.respondWith(
    caches.match(event.request)
      .then(response => response || fetch(event.request))
  );
});

// fitness-pwa-tracker/public/index.html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <link rel="manifest" href="./manifest.json" />
    <link rel="icon" href="favicon.ico" />
    <meta name="theme-color" content="#000000" />
    <title>Fitness Tracker PWA</title>
  </head>
  <body>
    <noscript>You need to enable JavaScript to run this app.</noscript>
    <div id="root"></div>
  </body>
</html>

// fitness-pwa-tracker/src/index.js
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';

ReactDOM.render(<App />, document.getElementById('root'));

if ('serviceWorker' in navigator) {
  window.addEventListener('load', () => {
    navigator.serviceWorker.register('/service-worker.js');
  });
}

// fitness-pwa-tracker/src/App.js
import React, { useState, useEffect } from 'react';
import './App.css';

function App() {
  const [workouts, setWorkouts] = useState(() => {
    const saved = localStorage.getItem('workouts');
    return saved ? JSON.parse(saved) : [];
  });

  const [newWorkout, setNewWorkout] = useState('');

  useEffect(() => {
    localStorage.setItem('workouts', JSON.stringify(workouts));
  }, [workouts]);

  const addWorkout = () => {
    if (newWorkout.trim() !== '') {
      setWorkouts([...workouts, { text: newWorkout, date: new Date().toLocaleString() }]);
      setNewWorkout('');
    }
  };

  return (
    <div className="App">
      <h1>Fitness Tracker PWA</h1>
      <input
        type="text"
        value={newWorkout}
        onChange={(e) => setNewWorkout(e.target.value)}
        placeholder="Enter your workout"
      />
      <button onClick={addWorkout}>Add Workout</button>

      <ul>
        {workouts.map((workout, index) => (
          <li key={index}>{workout.text} - {workout.date}</li>
        ))}
      </ul>
    </div>
  );
}

export default App;

// fitness-pwa-tracker/src/App.css
body {
  margin: 0;
  font-family: Arial, sans-serif;
  background-color: #f0f0f0;
}

.App {
  padding: 2rem;
  max-width: 600px;
  margin: auto;
  background: white;
  border-radius: 8px;
  box-shadow: 0 4px 6px rgba(0,0,0,0.1);
}

input {
  padding: 0.5rem;
  width: 70%;
  margin-right: 1rem;
}

button {
  padding: 0.5rem 1rem;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

ul {
  list-style: none;
  padding: 0;
}

li {
  background: #e9ecef;
  margin: 0.5rem 0;
  padding: 0.5rem;
  border-radius: 4px;
}

// fitness-pwa-tracker/src/index.css
* {
  box-sizing: border-box;
}
