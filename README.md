# GeoFire for Android/Java — Realtime location queries with Firebase

GeoFire is an open-source library for Android/Java that allows you to store and
query a set of items based on their geographic location.

GeoFire uses [Firebase](https://www.firebase.com/) for data storage, allowing
query results to be updated in realtime as they change.  GeoFire does more than
just measure the distance between locations; *it selectively loads only the
data near certain locations, keeping your applications light and responsive*,
even with extremely large datasets.

## Downloading GeoFire for Android/Java

In order to use GeoFire in your project, you need to [add the Firebase
SDK](https://www.firebase.com/docs/java-quickstart.html). There are then
multiple possibilities to use GeoFire in your project.

### Maven
*TODO: add to maven repository*

### Jar-File
You can also directly add the jar file in the folder `dist` to your project.

## API Reference

### GeoFire

A `GeoFire` object is used to read and write geolocation data to your Firebase
and to create queries.

#### Creating a new GeoFire instance

To create a new `GeoFire` instance you need to attach it to a Firebase
reference.

```java
GeoFire geoFire = new GeoFire(new Firebase("https://<your-firebase>.firebaseio.com/"));
```
Note that you can point your reference to anywhere in your Firebase, but don't
forget to [setup security rules for
GeoFire](https://github.com/firebase/geofire/blob/master/examples/securityRules/rules.json).

#### Setting location data
In GeoFire you can set and query locations by key. To set a location for a key
simply call the `setLocation` method. The location method is passed a key as
string and the location as latitude and longitude doubles.

```java
geoFire.setLocation("firebase-hq", 37.7853889, -122.4056973);
```

To remove a location and delete it from Firebase simply call
```java
geoFire.removeKey("firebase-hq");
```

#### Retrieving a loation
Retrieving locations happens with listeners. Like with any Firebase reference,
the listener is called once for the initial position and then for every update
of the location. Like that, your app can always stay up-to-date automatically.

```java
geoFire.addLocationEventListener("firebase-hq", new LocationEventListener() {
    @Override
    public void onLocationChanged(String key, double lat, double lng) {
        System.err.println(String.format("The location for key %s changed to [%f,%f]", key, lat, lng));
    }

    @Override
    public void onKeyRemoved(String key) {
        System.err.println(String.format("The location for key %s was remove", key));
    }

    @Override
    public void onCancelled(FirebaseError firebaseError) {
        System.err.println("There was an error reading data from Firebase: " + error);
    }
});
```

### Geo Queries

Locations can be queries with an `GeoQuery` object.

```java
// creates a new query around [37.7832, -122.4056] with a radius of 1000 meters
GeoQuery query = new GeoQuery(geoFire, 37.7832, -122.4056, 1000);
```

#### Receiving events for geo queries

There are 3 kind of events that can occur with a geo query:

1. **Key Entered**: The location of a key now matches the search criteria
2. **Key Exited**: The location of a key does not match the search criteria any more
3. **Key Moved**: The location of a key changed and the location still matches the search criteria

To listen for events you must add a `GeoQueryEventListener` to the `GeoQuery`.
```java
geoQuery.addGeoQueryEventListener(new GeoQueryEventListener() {
    @Override
    public void onKeyEntered(String key, double lat, double lng) {
        System.err.println(String.format("Key %s entered the search area at [%f,%f]", key, lat, lng));
    }

    @Override
    public void onKeyExited(String key) {
        System.err.println(String.format("Key %s is no longer in the search area"));
    }

    @Override
    public void onKeyMoved(String key, double lat, double lng) {
        System.err.println(String.format("Key %s moved within the search area to [%f,%f]", key, lat, lng));
    }
});
```

To remove the listener call either `removeListener` to remove a single event listener, or `removeAllListeners` to remove all event listeners for a `GeoQuery`.

#### Updating the search criteria

The GeoQuery search area can be changed with `setCenter` and `setRadius` Key
exited and key entered events will be triggered for keys moving in and out of
the old and new search area respecitively. No key moved events will be
triggered.

Updating the search area can be helpful for e.g. updating the query to the new visible map area after a user scrolls.

## Contributing

If you want to contribute to GeoFire for Java, just can clone the repository
and just start making pull requests.
```bash
git clone https://github.com/firebase/geofire-java.git
```