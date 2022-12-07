# RacePWN (Race Condition framework)

## 1. Description

RacePWN is a librace library and a racepwn utility that are designed to test a race condition attack through protocols that use a TCP connection.

### 1.1. librace

**librace** is a library written in the C programming language and designed to create and send multiple requests to a single host over the network.
Library support two race types:

1. **Parallel** - in this mode, a separate connection is created for each request. Requests are sent at the same time (using non-blocking socket write). Optionally, sending a request can be divided into 2 parts. The first is sending the main part of the request, the second is sending the last part of the request, after some time. The delay time as well as the size of the last part can be set.

2. **Pipeline** - in this mode, requests are glued together into one large request, which is sent through one connection.

### 1.2. racepwn

**racepwn** - a utility written in golang that implements the librace interface by setting parameters through a config written in json.

#### Configuration file example

```json
[
    {
        "race": {
            // Setting race parameters
            "type": "paralell", // race mode
            "delay_time_usec": 10000, // time delay between two request parts
            "last_chunk_size": 10 // last request chunck size
        },
        "raw": {
            "host": "tcp://localhost:8080", // hostname and port
            "ssl": false, // use ssl flag
            "race_param": [
                {
                    // race data parameters
                    "data": "GET / HTTP/1.1\r\nHost: example.com\r\n\r\n", // raw HTTP request
                    "count": 100 // packets count
                }
            ]
        }
    }
]
```

Two modes of operation are supported. Run as a console utility as well as a web service.

#### **console util:**

In this mode of operation, a configuration file is fed to the application input. The application makes requests to the server.

```
racepwn < config.json
```

#### **web service**:

In this mode, the utility starts working as a web service. To work, you need to make a `POST request`on the path `/race` with the contents of the configuration file.
The hostname and port must be specified using the `-hostname` flag.

```
racepwn -host "localhost:8080"
```

Example
```
curl -i -X POST localhost:8080/race --data @<(cat config.json)
```

## 2. Installing

### 2.1. Dependences

-   clang or gcc
-   golang
-   libevent (libevent-dev)
-   openssl (libssl-dev)
-   cmake

### 2.2. Build

To start the build process, run `./build.sh`.

_Warning_ : the build was tested only for linux.

### 2.3. Build for MacOS

1. Install dependencies

```sh
brew install go openssl libevent cmake gcc
```

2. Export LIBRARY_PATH

```sh
export LIBRARY_PATH=$LIBRARY_PATH:/usr/local/opt/openssl/lib/
```

3. Build
```sh
./macos_build.sh
```

### 2.4. Use with Docker
1. Build and run
```
docker build -t racepwn .
```

```
docker run -it racepwn bash
```

2. Launch the app
```
racepwn < /home/config.json
```


## 3. HOWTO

-   HTTP2 support.
-   HTTP response parsing.
-   Bindings for Python.
