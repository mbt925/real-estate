# Real Estate App
An offline first real estates app to showcase a microservices architecture, having clean architecture and TDD in mind.

- The app has a minimal material UI. The UI is fully implemented using Jetpack Compose and Compose navigation.
- The app provides two screens:
  - Listings: Displays list of real estates
  - Details: Displays the details of a single real estate
- The following errors are handled gracefully:
  - No internet connection
  - Unknown errors!
- Large screens are supported. The app will switch to a horizontal layout on larger screens, or in landscape mode of some devices.
- Offline support: The last displayed data is always cached locally and can be displayed when offline.


## Run the App
No authentication is required. Just update the sdk location and run the app.


## Architecture
- The app follows UDF (unidirectional data flow), using kotlin flow.
- It also respects separation of concerns, having data, api, feature, and ui layers.
- The architecture is based on microservices, MVI, and clean architecture.

### MVI
The MVI architecture is similar to that of Redux, yet simpler and more tuned to my taste.
The proposed MVI architecture introduces five entities:
- `State`: The single source of truth for data. UI only collects this single state and generates screens/views out of it.
- `UseCase`: Contains a piece of pure business logic that generates a flow of Effects.
- `Effect`: An entity that carries a bunch of data.
- `Reducer`: Receive a state and an effect, and generates a new state. Technically, it's a lambda like `(State, Effect) -> State`.
- `Context` -> Glues all the above-mentioned entities together. It contains the current `State`, executes a `UseCase`, passes the generated `Effect`s to the `Reducer`, and updates the `State`. In a nutshell: `UseCase -> [Flow<Effects>] -> Reducer -> [State]`

### Layers
- `Networking`: Is in charge of network communication, including headers interception, serialization adapters implementation, and generic response handling. All responses are translated to an `ApiResponse` object, with well-defined success and error cases.
- `Service`: Provides feature-specific remote and local services - namely, fetching real estates listings - using the networking module. Ideally, every feature should have a respective service module. However, connected features can share a single service module.
- `Design`: Implements the design language, including theming, components, and dimensions.
- `Feature`: Ideally, a feature module is a microservice which includes only a single feature which is a set of connected functionalities by definition. In case of the current app, only a single feature was needed, as such, it was named `feature`!
- `Plugins`: Provides version catalogs. It could also contain other plugins, e.g., lint, build, etc.

### Feature module
Technically, this module is a microservice. It's completely isolated from the app module. It has the following layers internally:
- `Api`: Exposes a couple of interfaces, explaining what's the expected behavior of this microservice. It also contains all the domain models.
- `Data`: Provides all the data that the domain layer expects, e.g., remote data, local data, etc..
- `Domain`: Provides an implementation to the api layer. Ideally, the domain module must be platform agnostic.
- `Feature`: This is the module that going to be exposed to the outside world. It's in charge of gluing all the layer together.
- `Demo`: This layer is not implemented, however, it can be easily. With this layer, a microservice can showcase it's functionalities using fake data and a demo app.

### Naming conventions
- `Network data model`s are suffixed with `Dto`
- `Local data model`s are suffixed with `Dao`
- `Domain data model`s have no suffix.
- `Ui data model`s are suffixed with `Param`
- `Mapper`s are in charge of mapping these models across layers

## Libraries
- [Retrofit](https://square.github.io/retrofit): A REST API communication framework
- [Coroutines](https://kotlinlang.org/docs/coroutines-overview.html): A asynchronous or non-blocking programming framework
- [OkHttp](https://square.github.io/okhttp): A HTTP client
- [Koin](https://github.com/InsertKoinIO/koin) A lightweight dependency injection (service locator) framework
- [Kotlin Serialization](https://kotlinlang.org/docs/serialization.html): A JSON serialization library
- [Mockk](https://mockk.io): A mocking framework
- [JetPack Compose](https://developer.android.com/jetpack/compose): Google's recommended declarative UI framework for building native UI
- [Navigation Compose](https://google.github.io/accompanist/navigation-animation/): Accompanist navigation compose library which is based on Jetpack navigation compose, and adds animations to it.


## Future Improvements
- All layers must be fully covered with unit/ui tests. Not all the layers are fully covered with tests at the moment.
- A plugin can be useful for aggregating the `build.gradle` boilerplate scripts.
