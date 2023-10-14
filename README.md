# RxCacheManager Flutter

RxCacheManager is a caching library for Flutter that provides a simple and flexible way to cache data and retrieve it efficiently. It uses a strategy-based approach, allowing you to define different caching strategies and apply them to your data. This README will guide you through the basic usage of RxCacheManager and how to integrate it into your Flutter project.

## Getting Started

To use RxCacheManager in your Flutter project, follow these steps:

1. Add the `rx_cache_manager` dependency to your `pubspec.yaml` file:

```yaml
dependencies:
  flutter:
    sdk: flutter
  rx_cache_manager: ^x.x.x # Replace with the latest version available
Run flutter pub get to fetch the new dependency.
```
Import the library in your Dart files:
```dart
import 'package:rx_cache_manager/rx_cache_manager.dart';
```
## Usage
RxCacheManager provides a caching mechanism for any type of data you want to cache. To use it, you need to define the data type you want to cache and its corresponding serializer and deserializer functions.

Example: Caching PayoutSettings
In this example, we will demonstrate how to cache and retrieve PayoutSettings data using RxCacheManager. First, let's define the PayoutSettings class and its serializer and deserializer functions:

```dart
class PayoutSettings {
  // Class implementation...

  factory PayoutSettings.fromMap(Map<String, dynamic> json) {
    // Deserialization logic...
  }

  Map<String, dynamic> toMap() {
    // Serialization logic...
  }
}
```
## Initializing the RxCacheManager
To start using RxCacheManager, you need to initialize it with an instance of CacheStorage. The CacheStorage is responsible for handling the actual storage of cached data. Here's an example of how to initialize RxCacheManager:

```dart
void main() async {
  final cache = RxCacheManager(CacheStorage("default"));
  // Other code...
}
```
## Caching Data
To cache data, you'll use the write method of the RxCacheManager instance. Here's an example of caching PayoutSettings data:

```dart
void main() async {
  final cache = RxCacheManager(CacheStorage("default"));

  // Caching PayoutSettings data
  final payoutSettings = PayoutSettings(
    id: "1234",
    interval: "monthly",
    // Other properties...
  );

  await cache.write("payout-settings", payoutSettings.toMap());

  // Other code...
}
```
## Retrieving Data
To retrieve data from the cache, you'll use the from method of the RxCacheManager instance along with a caching strategy. Here's an example of retrieving PayoutSettings data:

In this example, we used the CacheOrAsyncStrategy strategy, which first tries to fetch the data from the cache and, if not available or expired, falls back to an asynchronous data source.

```dart
void main() async {
  final cache = RxCacheManager(CacheStorage("default"));

  // Retrieving PayoutSettings data
  final request = await cache
      .from<PayoutSettings, Map>("payout-settings")
      .withAsync(() => Future.delayed(
            const Duration(seconds: 2),
            () => {
                  "id": "1234",
                  "interval": "monthly",
                  // Other properties...
                },
          ))
      .withSerializer((json) => PayoutSettings.fromMap(json))
      .withStrategy(CacheOrAsyncStrategy())
      .withTtl(30000000)
      .execute();

  print(request?.toMap());

  // Other code...
}
```

### JustCacheStrategy
The JustCacheStrategy fetches data from the cache and doesn't attempt to update it even if it's expired.

```dart

final request = await cache
    .from<PayoutSettings, Map>("payout-settings")
    .withStrategy(JustCacheStrategy())
    .execute();
```
JustAsyncStrategy
The JustAsyncStrategy fetches data from the asynchronous source and doesn't attempt to cache it.

```dart

final request = await cache
    .from<PayoutSettings, Map>("payout-settings")
    .withAsync(() => fetchPayoutSettingsFromServer()) // Replace with your async function
    .withStrategy(JustAsyncStrategy())
    .execute();
```
### CacheOrAsyncStrategy
The CacheOrAsyncStrategy first tries to fetch the data from the cache and, if not available or expired, falls back to an asynchronous data source.

```dart

final request = await cache
    .from<PayoutSettings, Map>("payout-settings")
    .withAsync(() => fetchPayoutSettingsFromServer()) // Replace with your async function
    .withStrategy(CacheOrAsyncStrategy())
    .execute();
```
### CacheAndAsyncStrategy
The CacheAndAsyncStrategy always returns the cached content and tries to update it if the cache expires.

```dart

final request = await cache
    .from<PayoutSettings, Map>("payout-settings")
    .withAsync(() => fetchPayoutSettingsFromServer()) // Replace with your async function
    .withStrategy(CacheAndAsyncStrategy())
    .execute();
```
### AsyncOrCacheStrategy
The AsyncOrCacheStrategy first tries to fetch the data from the asynchronous source and, if unsuccessful, falls back to the cache.

```dart

final request = await cache
    .from<PayoutSettings, Map>("payout-settings")
    .withAsync(() => fetchPayoutSettingsFromServer()) // Replace with your async function
    .withStrategy(AsyncOrCacheStrategy())
    .execute();
```
## Clearing the Cache
To clear the cache, you can use the clear method of the RxCacheManager instance. You can optionally specify a prefix to clear only specific cached items. Here's how you can clear the cache and retrieve cache size:

```dart

void main() async {
  final cache = RxCacheManager(CacheStorage("default"));

  // Clear the entire cache
  await cache.clear();
  // Get the entire cache size
  int size = await cache.size();
  // Clear cache with a specific prefix
  await cache.clear(prefix: "payout-settings");

  // Other code...
}
```
## Contribution
The code was written by Michael Piper (https://github.com/michaelpiper)
Contributions to the RxCacheManager package are welcome! If you encounter any issues or have suggestions for improvements, please open an issue or submit a pull request.

## License
This package is released under the MIT License.

## Conclusion
RxCacheManager provides a powerful caching solution for your Flutter applications. By defining caching strategies and using serializers, you can efficiently cache and retrieve data, improving the performance and responsiveness of your app. For more information and advanced usage, check the full API documentation and examples.

Happy coding!