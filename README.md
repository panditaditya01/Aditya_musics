# Aditya_musics
Here you can find any type of music according to your taste by searching it 
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Music Streaming with Login</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <h1>Stream Unlimited Songs</h1>
    
    <div id="loginSection">
        <input type="text" id="username" placeholder="Username">
        <input type="password" id="password" placeholder="Password">
        <button onclick="login()">Login</button>
        <button onclick="register()">Register</button>
    </div>

    <div id="musicSection" style="display:none;">
        <h2>Search Music</h2>
        <input type="text" id="searchBox" placeholder="Type song or artist...">
        <button onclick="searchMusic()">Search</button>
        <div id="results"></div>

        <h2>Your Playlist</h2>
        <div id="playlist"></div>
        <audio id="audioPlayer" controls></audio>
    </div>

    <script src="script.js"></script>
</body>
</html>
body {
    font-family: Arial, sans-serif;
    background-color: #f4f4f4;
    text-align: center;
    padding: 20px;
}

h1, h2 {
    color: #333;
}

input, button {
    padding: 10px;
    margin: 10px;
    font-size: 16px;
}

#results div, #playlist div {
    background-color: #fff;
    border: 1px solid #ddd;
    padding: 10px;
    margin: 5px;
    cursor: pointer;
}

#results div:hover, #playlist div:hover {
    background-color: #f0f0f0;
}

audio {
    margin-top: 20px;
}
let users = {};
let playlist = [];

function register() {
    const username = document.getElementById('username').value;
    const password = document.getElementById('password').value;
    
    if (users[username]) {
        alert('Username already exists!');
    } else {
        users[username] = password;
        alert('Registration successful. Please login.');
    }
}

function login() {
    const username = document.getElementById('username').value;
    const password = document.getElementById('password').value;
    
    if (users[username] && users[username] === password) {
        alert('Login successful!');
        document.getElementById('loginSection').style.display = 'none';
        document.getElementById('musicSection').style.display = 'block';
    } else {
        alert('Invalid login credentials.');
    }
}

function searchMusic() {
    const query = document.getElementById('searchBox').value;
    const results = document.getElementById('results');
    results.innerHTML = 'Searching...';

    fetch(`https://api.spotify.com/v1/search?q=${query}&type=track&limit=5`, {
        headers: {
            'Authorization': 'Bearer YOUR_SPOTIFY_ACCESS_TOKEN'
        }
    })
    .then(response => response.json())
    .then(data => {
        results.innerHTML = '';
        data.tracks.items.forEach(item => {
            const song = item.name;
            const artist = item.artists[0].name;
            const preview = item.preview_url;
            
            const div = document.createElement('div');
            div.innerHTML = `
                <strong>${song}</strong> by ${artist}<br>
                <button onclick="playMusic('${preview}')">Play</button>
                <button onclick="addToPlaylist('${song}', '${preview}')">Add to Playlist</button>
            `;
            results.appendChild(div);
        });
    })
    .catch(error => {
        results.innerHTML = 'No results found.';
    });
}

function playMusic(url) {
    const player = document.getElementById('audioPlayer');
    player.src = url;
    player.play();
}

function addToPlaylist(song, url) {
    playlist.push({ song, url });
    updatePlaylist();
}

function updatePlaylist() {
    const playlistDiv = document.getElementById('playlist');
    playlistDiv.innerHTML = '';

    playlist.forEach((item, index) => {
        const div = document.createElement('div');
        div.innerHTML = `
            ${item.song}
            <button onclick="playMusic('${item.url}')">Play</button>
            <button onclick="removeFromPlaylist(${index})">Remove</button>
        `;
        playlistDiv.appendChild(div);
    });
}

function removeFromPlaylist(index) {
    playlist.splice(index, 1);
    updatePlaylist();
}
