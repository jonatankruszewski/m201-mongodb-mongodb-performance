# CHAPTER 1

PERFORMANCE. 5 CHAPTER. INTERMIEADTE. DEVELOPMENT AND OPERATIONS TEAMS.
OVERVIEW
INDEXES
INDEX OPERATIONS
CRUD OPTIMIZATIONS
DISTRIBUTED SYSTEMS

## LECTURE HARDWARE CONSIDERATIONS AND CONFIGURATIONS

VON NEUMANN ARCHITECTURE
CPU FOR CALCULATIOS, MEMORY FOR EXECUTIONS.

RAM
AGGRETGATION
INDEX TRAVERSIING
WRITE OPERATIONS
QUERY ENGINE
CONNECTIONS.

MORE RAM MORE PERFORMANCE.

CPU
STORAGE ENGINE
CONCURRENCY LEVEL.
MONGO WILL TRY TO USE ALL THE CORES.

MORE CPU CORES, MORE PERFORMANCE.
PAGE COMPRESSION
DATA CALCUATION
AGGREGATION FRAMEWORK OPERATIONS
MAP REDUCE

REQUIRE CPU CYCLES

## LECTURE HARDWARE CONSIDERATIONS AND CONFIGURATIONS PART 2

NOT ALL WRITES AND READS ARE NON LOCKING.

RECOMMENDED ARCHITECTURE RAID FOR MONGODB DEPLOYMENTS: 10.
RAID 5 AND 6 NOT RECOMMENDED.
RAID 0 PROVIDES GOOD WRITES BUT NOT GOOD READS.

Check that:
Brew is installed

MongoDB Enterprise is installed. [link](https://www.mongodb.com/try/download/enterprise)

MongoDB Shell is installed. [link](https://www.mongodb.com/try/download/shell)
Or:
brew install mongodb/brew/mongodb-community-shell

MongoDB Database tools is installed. [link](https://www.mongodb.com/try/download/database-tools)
After downloading, cd into the /bin folder and copy the files to /usr/local/bin

```js
sudo cp * /usr/local/bin/
```

To chech brew services (mongodb community should be listed):

```js
brew services list
```

To start mongodb:

```js
brew services start mongodb-community
```

To start the mongoShell:

```js
mongo
```

To stop mongodb:

```js
brew services stop mongodb-community
```

To bring the people.json file:
mongoimport --drop -c people --uri mongodb+srv://user:password@m201.g9a0m.mongodb.net/test /Users/jkru/DATA/DEV/JK/m201MongoDB/people.json

asd