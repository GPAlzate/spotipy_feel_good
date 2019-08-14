# Spotify feel good experimenting
#### Just playing around to see how accurate [this formula](http://www.jolij.com/the-feel-good-song-formula/) is.

## What the notebook looks like so far:

```

token = util.prompt_for_user_token(username, scope, client_id=client_id,client_secret=client_secret,redirect_uri=ruri)
sp = spotipy.Spotify(auth=token)
results = sp.current_user_saved_tracks(limit=50)

artists = []
songs = []
ids = []
tracks = []

for item in results['items']:
    track = item['track']
    tracks.append(track)
    artists.append(track['artists'][0]['name'])
    songs.append(track['name'])
    ids.append(track['id'])
    
df = pd.DataFrame(sp.audio_features(ids))

#because only 50 per request max
offset = 50
results = sp.current_user_saved_tracks(limit=50, offset=50)

while(len(results['items'])):
    ids = []
    for item in results['items']:
        track = item['track']
        tracks.append(track)
        ids.append(track['id'])
        songs.append(track['name'])
        artists.append(track['artists'][0]['name'])
    df = df.append(pd.DataFrame(sp.audio_features(ids)), ignore_index = True)
    offset = offset + 50
    results = sp.current_user_saved_tracks(limit=50,offset=offset)

library = df[['acousticness', 'danceability', 'energy','instrumentalness','key','liveness','loudness','mode','speechiness','tempo','valence']].copy()
library['song_title'] = songs
library['artist'] = artists

#test view
library[library.song_title == "Mr. Blue Sky"].iloc[0]

```

## The `feelgood()` function.
```

#TODO this idk how to figure out num chords
def feelgood(track):
    tempo = track['tempo']
    key = track['key']
    nChords = 0
    return 60 + ((0.00165 * tempo – 120) ** 2) + (4.376 * key) + 0.78 * nChords – (key * nChords)

```
