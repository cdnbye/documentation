## 0.5.0
- loadtimeout adaptive

## 0.5.1
- optimize loadtimeout adaptive

## 0.5.2
- fix bug when re-instantiate Hls, signaling is switched to default setting

## 0.5.3
- fix a bug may block p2p
- max buffer size setting for mobile

## 0.5.4
- detect and report player

## 0.6.0
- pre-buffer for smooth playing
- Add new field `live`

## 0.7.0
- Add `validateSegment` [funtion](https://docs.cdnbye.com/#/en/API?id=how-to-check-segment-validity)

## 0.8.0
- Add `prefetchHttpSegments`: The number of segments that will be forced to download by HTTP at the beginning
- Add `getStats`function in p2pConfig

## 0.8.1
- Restart P2P if the node expired