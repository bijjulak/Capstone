# Music playlist recommendation model

***Link to notebook:*** 


## Project Overview

The objective is to build a recommendation system that can suggest a collection of songs to form a personalized playlist that can include songs that a user typically enjoys and songs that are different from their usual preferences but are likely to enjoy. This is done using data obtained from the Milion Playlist Dataset from Spotify, Inc. The dataset consists of a million user generated playlists. And using this data, if we were given a playlist of a random user, then this model should be able to recommend additional tracks to the user to continue listening as they reach the end of the playlist. In order to achieve this, I first developed a baseline model that uses the most popular songs across the dataset and uses the first 500 songs to continue the playlist. The next improvement on this algorithm is to use a collaborative (user-based) filtering algorithm. There is insufficient information in the dataset to make a item-based collaborative filtering algorithm, unluess you use the Spotify API to scrape through the Spotify database to get attributes for each song. As in any recommendation algorithm, I compiled the data into a Sparse Matix and calculated the similarities between playlists. This algorithm was able to predict songs with ~65% accuracy compared to the baseline model of ~14%.

## Business objective

As any normal listener stuck on a music app listening to endless repeats of the same song will attest, the task of enjoying music becomes a very frustrating experience and engagement with the app falls off a cliff. This project will strive to deliver a basic recommendation algorithm that will keep the user engaged and deliver an enjoyable experience listening to music and changing the way consumers interact with their audio content. 

## Data Source(s) and structure:

The dataset used will be the "Million Playlist dataset" from spotify.

https://www.aicrowd.com/challenges/spotify-million-playlist-dataset-challenge/dataset_files

From Spotify's description - " Sampled from the over 4 billion public playlists on Spotify, this dataset of 1 million playlists consist of over 2 million unique tracks by nearly 300,000 artists, and represents the largest public dataset of music playlists in the world. The dataset includes public playlists created by US Spotify users between January 2010 and November 2017. "

The dataset consists of 1000 json files consisting of 1000 playlists each. Each playlist in the dataset contains a playlist title, the track list (including track IDs and metadata), and other metadata fields (last edit time, number of playlist edits, and more). 

## EDA
### Data Description
For this project, I considered only 50 files for training. This equeates to 50000 playlists and approximately 450000 tracks. The 50 files were chose at random. Each playlist object contains the following attributes:

* ***pid*** - integer - playlist id - the MPD ID of this playlist. This is an integer between 0 and 999,999.
* ***name*** - string - the name of the playlist 
* ***description*** - optional string - if present, the description given to the playlist.  Note that user-provided playlist descrptions are a relatively new feature of Spotify, so most playlists do not have descriptions.
* ***modified_at*** - seconds - timestamp (in seconds since the epoch) when this playlist was last updated. Times are rounded to midnight GMT of the date when the playlist was last updated.
* ***num_artists*** - the total number of unique artists for the tracks in the playlist.
* ***num_albums*** - the number of unique albums for the tracks in the playlist
* ***num_tracks*** - the number of tracks in the playlist
* ***num_followers*** - the number of followers this playlist had at the time the MPD was created. (Note that the follower count does not including the playlist creator)
* ***num_edits*** - the number of separate editing sessions. Tracks added in a two hour window are considered to be added in a single editing session.
* ***duration_ms*** - the total duration of all the tracks in the playlist (in milliseconds)
* ***collaborative*** -  boolean - if true, the playlist is a collaborative playlist. Multiple users may contribute tracks to a collaborative playlist.
* ***tracks*** - an array of information about each track in the playlist. Each element in the array is a dictionary with the following fields:
   * ***track_name*** - the name of the track
   * ***track_uri*** - the Spotify URI of the track
   * ***album_name*** - the name of the track's album
   * ***album_uri*** - the Spotify URI of the album
   * ***artist_name*** - the name of the track's primary artist
   * ***artist_uri*** - the Spotify URI of track's primary artist
   * ***duration_ms*** - the duration of the track in milliseconds
   * ***pos*** - the position of the track in the playlist (zero-based)

### Visualizations
Some basic visualization of the data

***Top 20 artists with the most number of tracks***
![](/Artists_with_most_tracks.png)

It seems like classical music trumps everyone else. 

***Top 20 artists by most number of playlist appearances***
![](/Atists_in_most_playlists.png)

The artist "Drake" is the most popular artist on Spotify by a wide margin.

***Most popular track***
![](/Most_popular_tracks.png)

Looking at the most popular songs, pop music tracks dominate the list.

***Distribution of Number of Tracks in a Playlist***
![](/distribution_of_tracks.png)

The distribution of th enumber of songs across the playlists vary by a wide margin. So I decided to go with the requirement for the Spotify challenge to recommend 500 songs for each playlist.

## Test dataset

For the test dataset, I pick another json file (which is not in the original 50) and then picked 30 playlists by going through the file sequentially. A check was doen to make sure that all the songs in these 30 playlists existed in the original 50000 playlists. If we cannot find 30 playlists satisfying this condition, then another json file was chosen and playlists were sampled until we get 30 playlists. For these 30 playlists, I obscured 50% of the tracks and predictions were made based on the remaining 50% of the tracks. The predicted tracks were compared to the obscured list to see how accurate the prediction is.

## Models
### Baseline Model
For the baseline model, recommendations are done by finding the most popular songs across the 50000 playlists base on the total number of appearances. The songs were ordered based on the number of appearances and the top 500 songs were recommended. 

### Collaborative Filtering (user-based)
The next step was to use a collaborative filtering model, where playlists are grouped according to their similarities and then songs are recommended to a given playlist based on the popularity of the non-overlapping songs from playlists in the same group.

In order to compare the similarities of the playlists, a sparse matrix of the playlists and tracks were created, i.e. all the playlists were listed as rows and all the tracks as columns. For every playlist, if a track existed in the playlist then the cell corresponding to that particular playlist and track was encoded a 1. The rest of the cells were encoded as 0. After creating this sparse-matrix, the neighborhood and similarity of a particular playlist was calculated the k-neighbors model in sklearn and the cosine similarity for the distance metric. Further optimizations were done by varying the number of neighbors in the model.

## Results
Recommendation Generator
As seen from the plot below, the n-neighbors algorithm is able to recommend songs with a >50% accuracy and is much better than the baseline model accuracy of ~14%

## Conclusions
The goal this project was to create a recommendation system that would extend the listening queue of a given playlist. Based on a literature search, the most reasonable algorith was a user-based collaborative filtering algorithm. Ofcourse, this project is barely even scratching the surface. But still, if I had an algorithm that could recommend songs based on a 65% hit-rate, I would be pretty satisfied compared to the stuff I deal with. I am sure there are more complex algorithms available, but they are beyond my limited coding skills considering I learned to code 6 months back. 

## Next steps
The is an API from spotify which allows you to scrape data for each track. It lists attributes such as danceability, tempo, etc. for each track. This would open up the possibility of doing item-based collaborative filterin which I think is more powerful. This was my original objective, but I realized I was over-reaching with my coding proficiency.
