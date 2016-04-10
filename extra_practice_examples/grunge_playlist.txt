SELECT 
Playlist.Name as PLAYLIST_NAME,
Artist.Name as ARTIST_NAME,
Track.Name as TRACK_NAME,
Album.Title as ALBUM_TITLE

FROM Playlist INNER JOIN PlaylistTrack
ON Playlist.PlaylistId = PlaylistTrack.PlaylistId

INNER JOIN Track
ON PlaylistTrack.TrackId = Track.TrackId

INNER JOIN Album
ON Track.AlbumId = Album.AlbumId

INNER JOIN Artist
ON Album.ArtistId = Artist.ArtistId

WHERE Playlist.PlaylistId = 16
