#### Firebase :

Firebase is a backend as a service, which does provide a cloud database (including offline support) and storage.
The benefit of using firebase database is that if there is no internet connection then it will store the data locally until internet connection becomes available and then syncs it with the server. In this way you will not loose your data even if user uninstalls the app or clears the cache.



- keepSynced(true) effectively keeps a listener active on the given reference, from the moment it's called, for as long as the app is running, so that the local version of the data is always in sync with the remote version on the server.

- setPersistenceEnabled(true) just activates local caching of the data read by the SDK. When persistence is enabled, the app can still query data previously read. It takes effect for all data read by the SDK. While persistence is enabled, you can't control which data is cached - all read data is cached up to 10MB max. When the max is reached the oldest data will be evicted from the cache.

- If you are using FirebaseDatabase.getInstance().setPersistenceEnabled(true); means that Firebase will create a local copy of your database which also means that every change that is made while you are offline, will be added to a queue. So, as this queue grows, local operations and application startup will slow down. So the speed depends on the dimension of that queue. But rememeber, Firebase is designed as an online database that can work for short to intermediate periods of being disconnected and not as an offline database.

- https://firebase.google.com/pricing/?gclid=EAIaIQobChMIqqypm7vK3wIVHbbACh1oBwLzEAAYASAAEgK57fD_BwE

#### Realm :

Realm database was built from scratch for offline and real-time applications running on mobile and wearable devices. Realm is not an ORM or built on top of SQLite, it has its own database engine and doesn’t just rely on key-value stores.

That explains why developers prefer to use Realm while handling a large amount of data or for high-performance applications. Realm allows developers to undergo frequently mapping classes, tables, foreign keys or the fields.

Realm is an object-oriented database rather than a relational database. OO model makes it 10x faster than SQLite and saves you from running tons of query which is a common thing for a typical SQL database.




#### Google firebase  vs Realm  :
- Realm Mobile Platform is entirely free as long as you use your own server. Firebase is not entirely free: https://firebase.google.com/pricing/

- Firebase Realtime database is a remote database for storing the entire application’s data while realm is mostly used for localdata where you say one person’s data in phone and make it work even when the app is offline.

- - http://offlinefirst.org/sync/

![Alt Text](https://github.com/vaquarkhan/vaquarkhan/blob/master/Design/Untitled.png)

---------------------------------------------------------------------------
#### Selection criteria 

- Offline-first
Does it supports offline feature in the app? How does it handle synchronisations and conflict across multiple devices?

- Database security
Does it supports AES 256 level of encryption and decryption? Would I be able to implement a secure authentication?

- App performance
To evaluate the performance, we took many factors like read/write speed, zero copy design, storage engine, and how does it handle concurrency control?

- Industry regulations
Large organisations have to adhere to industry regulations and compliances in order to deploy their application securely. Learn which databases comply with regulations, and which don't.

- Supported file and data types
We explored the data types(Bool, Int, Int8, Int16, Int32, Int64), and file types(images, videos), which are supported by these databases.

- Pricing
Some databases are open source and free to use like SQLite, and PouchDB. But, databases like Realm, and Firebase charge for their additional features. Find out how database pricing stacks up to your needs.

- Real time sync
For Streaming, and gaming applications, Real Time Sync is crucial. Learn which database is more suitable when you need to sync the data in real time.

- Compatibility with third party libraries, and Native modules.
There are tons of third party libraries available for React Native to customise the application for a more richer experience. Find out which database provides more flexibility to build your app.

---------------------------------------------------------------------------

#### Architectural differences

- https://www.azilen.com/blog/realm-mobile-platform-real-time-data-synchronization/
- https://www.codementor.io/dhananjaypatel/firebase-authentication-database-all-that-counts-65mwkxp3w

---------------------------------------------------------------------------

#### ROOM google library to store data 
- https://developer.android.com/training/data-storage/room/

#### Firebase vs Firestore
- https://medium.com/@siwakornsittilerdpaisal/realtime-database-vs-cloud-firestore-3cad1eeed0f4
