### 0.5.0
- loadtimeout adaptive

### 0.5.1
- optimize loadtimeout adaptive

### 0.5.2
- fix bug when re-instantiate Hls, signaling is switched to default setting

### 0.5.3
- fix a bug may block p2p
- max buffer size setting for mobile

### 0.5.4
- detect and report player

### 0.6.0
- pre-buffer for smooth playing
- Add new field `live`

### 0.7.0
- Add `validateSegment` [funtion](https://docs.cdnbye.com/#/en/API?id=how-to-check-segment-validity)

### 0.8.0
- Add `prefetchHttpSegments`: The number of segments that will be forced to download by HTTP at the beginning
- Add `getStats`function in p2pConfig

### 0.8.1
- Restart P2P if the node expired
- Increase buffer size

### 0.9.0
- Optimize P2P algorithm
- Solved the buffer stall problem in live

### 0.9.1
- Fix data reporting bug
- Remove `prefetchHttpSegments`

### 0.9.2
- Parameter optimization
- Fixed bugs in clappr-plugin

### 0.9.4
- live mode optimization
- fix known bug

### 0.10.2
- Optimize P2P algorithm
- fix data report bug

### 0.10.5  13/8/2019
- live mode optimization
- fix known bug

### 0.11.0 20/8/2019
- Optimize P2P algorithm

### 0.11.1-0.11.2 25/8/2019
- fix bug when using encrypted hls