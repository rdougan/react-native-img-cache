# React Native Image Cache

[![CircleCI](https://circleci.com/gh/wcandillon/react-native-img-cache.svg?style=svg)](https://circleci.com/gh/wcandillon/react-native-img-cache)
[![npm version](https://badge.fury.io/js/react-native-img-cache.svg)](https://badge.fury.io/js/react-native-img-cache)

CachedImage component and Cache image manager for React Native. Here are several articles on RN and image processing:
* [Image Caching in React Native](https://hackernoon.com/image-caching-in-react-native-96d8df33ca84)
* [Image Pipeline with React Native ListView](https://hackernoon.com/image-pipeline-with-react-native-listview-b92d4768b17c)
* [Firebase Live Pagination](https://medium.com/@wcandillon/firebase-live-pagination-474748853e52)

## Why do I need this?
Starting version `0.43`, the React Native [Image component](https://facebook.github.io/react-native/docs/image.html) now has a cache property: `cache: force-cache`. This is a major improvement, however at this time, I wasn't able to use it in a way that provides a smooth user experience as good as this module. This module provides also more control over the way images are downloaded via `cancel()`. This can be extremely useful when scrolling quickly through a list of images.


## Installation

### react-native-fetch-blob
This package has a dependency with [react-native-fetch-blob](https://github.com/wkh237/react-native-fetch-blob).
If your project doesn't have a dependency with this package already, please refer to [their installation instructions](https://github.com/wkh237/react-native-fetch-blob#user-content-installation).

```bash
npm install react-native-img-cache --save
```

## Usage

### CachedImage

The `CachedImage` component assumes that the image URI will never change. The image is stored and served from the application cache.

```jsx
import {CachedImage} from "react-native-img-cache";

<CachedImage source={{ uri: "https://i.ytimg.com/vi/yaqe1qesQ8c/maxresdefault.jpg" }} />
```

The `mutable` property implies assumes that the image URI can change over time. The lifetime of this cache is the one of the running application and it can be manually busted using `ImageCache`.

```jsx
import {CachedImage} from "react-native-img-cache";

<CachedImage source={{ uri: "https://i.ytimg.com/vi/yaqe1qesQ8c/maxresdefault.jpg" }} mutable />
```

### Custom Image Component

By default, the `CachedImage` component is using the [standard RN Image component](https://facebook.github.io/react-native/docs/image.html).
It is possible however to use a different component via `CustomCachedImage`. In the example below, we use the `Image` component from [react-native-image-progress](https://github.com/oblador/react-native-image-progress).

```jsx
import {CustomCachedImage} from "react-native-img-cache";

import Image from 'react-native-image-progress';
import ProgressBar from 'react-native-progress/Bar';

<CustomCachedImage
  component={Image}
  source={{ uri: 'http://loremflickr.com/640/480/dog' }} 
  indicator={ProgressBar} 
  style={{
    width: 320, 
    height: 240, 
  }}/>
```

### ImageCache

#### clear()

Remove cache entries and all physical files.

```js
ImageCache.get().clear();
```

#### bust(uri)

`ImageCache` can be used to bust an image from the local cache.
This removes the cache entry but it **does not remove any physical files**.

```js
ImageCache.get().bust("https://i.ytimg.com/vi/yaqe1qesQ8c/maxresdefault.jpg");
```

#### cancel(uri)

It can also be used to cancel the download of an image. This can be very useful when [scrolling through images](https://medium.com/@wcandillon/image-pipeline-with-react-native-listview-b92d4768b17c).

```js
ImageCache.get().cancel("https://i.ytimg.com/vi/yaqe1qesQ8c/maxresdefault.jpg");
```

#### on(uri, observer, immutable)

The `ImageCache` class can register observers to the cache.

```js
const immutable = true;
const observer = (path: string) => {
    console.log(`path of the image in the cache: ${path}`);
};
ImageCache.get().on(uri, observer, immutable);
```

We use the observer pattern instead of a promise because a mutable image might have different version with different paths in the cache.

#### dispose(uri, observer)

Observers can be deregistered using `dispose`:

```js
ImageCache.get().dispose(uri, observer);
```
