[![CircleCI](https://circleci.com/gh/krzysztofzablocki/Sourcery.svg?style=shield)](https://circleci.com/gh/krzysztofzablocki/Sourcery)
[![codecov](https://codecov.io/gh/krzysztofzablocki/Sourcery/branch/master/graph/badge.svg)](https://codecov.io/gh/krzysztofzablocki/Sourcery)
[![docs](https://cdn.rawgit.com/krzysztofzablocki/Sourcery/master/docs/badge.svg)](https://cdn.rawgit.com/krzysztofzablocki/Sourcery/master/docs/index.html)
[![Version](https://img.shields.io/cocoapods/v/Sourcery.svg?style=flat)](http://cocoapods.org/pods/Sourcery)
[![License](https://img.shields.io/cocoapods/l/Sourcery.svg?style=flat)](http://cocoapods.org/pods/Sourcery)
[![Platform](https://img.shields.io/cocoapods/p/Sourcery.svg?style=flat)](http://cocoapods.org/pods/Sourcery)

<img src="Resources/icon-128.png">

**Sourcery** is a code generator for Swift language, built on top of Apple's own SourceKit. It extends the language abstractions to allow you to generate boilerplate code automatically.

It's used in over 6000 projects on both iOS and macOS and it powers some of the most popular and critically-acclaimed apps you have used. Its massive community adoption was one of the factors that pushed Apple to implement derived Equality and automatic Codable conformance. Sourcery is maintained by a growing community of [contributors](https://github.com/krzysztofzablocki/Sourcery/graphs/contributors).

Try **Sourcery** for your next project or add it to an existing one -- you'll save a lot of time and be happy you did!

## TL;DR
Sourcery allows you to get rid of repetitive tasks. An example might be implementing `Equatable`, without Sourcery you need to implement stuff like this:

```swift
extension Person: Equatable {
    static func ==(lhs: Person, rhs: Person) -> Bool {
        guard lhs.firstName == rhs.firstName else { return false }
        guard lhs.lastName == rhs.lastName else { return false }
        guard lhs.birthDate == rhs.birthDate else { return false }
        return true
    }
}
``` 

This is trivial code but imagine doing this across ten types. Across fifty. How many structs and classes are in your project? 

Sourcery removes the need to write this code. And if you refactor or add properties, the equality code will be automatically updated for you, eliminating possible human errors. 

Sourcery automation can be applied to many more domains, e.g.

- Equality & Hashing
- Enum cases & Counts
- Lenses
- Mocks & Stubs
- LinuxMain
- Decorators
- JSON coding
- NSCoding and Codable

It's trivial to write new templates to remove boilerplate that is specific to your projects.

## How To Get Started
There are plenty of tutorials for different uses of Sourcery:

- [The Magic of Sourcery](https://www.caseyliss.com/2017/3/31/the-magic-of-sourcery) is a great starting tutorial
- [Generating Swift Code for iOS](https://www.raywenderlich.com/158803/sourcery-tutorial-generating-swift-code-ios) deals with JSON handling code
- [How To Automate Swift Boilerplate with Sourcery](https://atomicrobot.io/blog/sourcery/) generates conversions to dictionaries
- [Codable Enums](https://littlebitesofcocoa.com/318-codable-enums) implements Codable support for Enumerations
- [Building an API client with Sourcery](https://littlebitesofcocoa.com/295-building-an-api-client-with-sourcery-key-value-annotations) builds API client leveraging Sourcery annotations
- [Metaprogramming in Swift](https://www.youtube.com/watch?v=Ukm70Ibk_bY) is a video from CocoaHeads where Krzysztof introduces Sourcery

## Installation

- _Binary form_

    Downloadthe  latest release with the prebuilt binary from [release tab](https://github.com/krzysztofzablocki/Sourcery/releases/latest). Unzip the archive into the desired destination and run `bin/sourcery`

- _CocoaPods_

    Add pod 'Sourcery' to your Podfile and run `pod update Sourcery`. This will download the latest release binary and will put it in your project's CocoaPods path so you will run it with `$PODS_ROOT/Sourcery/bin/sourcery`

- _Building from source_

    Download the latest release source code from [the release tab](https://github.com/krzysztofzablocki/Sourcery/releases/latest) or clone the repository and build Sourcery manually.

    - _Building with Swift Package Manager_

        Run `swift build -c release` in the root folder. This will create a `.build/release` folder and will put the binary there. Move the **whole `.build/release` folder** to your desired destination and run with `path_to_release_folder/sourcery`

        > Note: Swift and JS templates are not supported when building with SPM yet.

    - _Building with Xcode_

        Open `Sourcery.xcworkspace` and build with `Sourcery-Release` scheme. This will create `Sourcery.app` in the Derived Data folder. You can copy it to your desired destination and run with `path_to_sourcery_app/Sourcery.app/Contents/MacOS/Sourcery`

## Documentation

Full documentation is available [here](https://cdn.rawgit.com/krzysztofzablocki/Sourcery/master/docs/index.html).

## Usage

Sourcery is a command line tool, you can either run it manually or in a custom build phase using the following command:

```
$ ./sourcery --sources <sources path> --templates <templates path> --output <output path>
```

> Note: this command may be different depending on the way in which you installed Sourcery (see [Installing](#installing))

### Command line options

- `--sources` - Path to a source swift files. You can provide multiple paths using multiple `--sources` option.
- `--templates` - Path to templates. File or Directory. You can provide multiple paths using multiple `--templates` options.
- `--output` [default: current path] - Path to output. File or Directory.
- `--config` [default: current path] - Path to config file. Directory. See [Configuration file](#configuration-file).
- `--args` - Additional arguments to pass to templates. Each argument can have an explicit value or will have implicit `true` value. Arguments should be separated with `,` without spaces (i.e. `--args arg1=value,arg2`). Arguments are accessible in templates via `argument.name`
- `--watch` [default: false] - Watch both code and template folders for changes and regenerate automatically.
- `--verbose` [default: false] - Turn on verbose logging
- `--quiet` [default: false] - Turn off any logging, only emit errors
- `--disableCache` [default: false] - Turn off caching of parsed data
- `--prune` [default: false] - Prune empty generated files
- `--version` - Display the current version of Sourcery
- `--help` - Display help information

### Configuration file

You can also provide arguments using `.sourcery.yml` file in project's root directory, like this:

```yaml
sources:
  - <sources path>
  - <sources path>
templates:
  - <templates path>
  - <templates path>
output:
  <output path>
args:
  <name>: <value>
```

You can exlude some sources or templates using `include` and `exclude` keys:

```yaml
sources:
  include:
    - <sources path to include>
    - <sources path to include>
  exclude:
    - <sources path to exclude>
    - <sources path to exclude>
templates:
  include:
    - <templates path to include>
    - <templates path to include>
  exclude:
    - <templates path to exclude>
    - <templates path to exclude>
```

You can provide either sources paths or targets to scan:

```yaml
project:
  file:
    <path to xcodeproj file>
  root:
    <path to project sources root>
  target:
    name: <target name>
    module: <module name> //required if different from target name
```

You can use several `project` or `target` objects to scan multiple targets from one project or to scan multiple projects.

> Note: Paths in configuration files are by default relative to configuration file path. If you want to specify absolute paths start it with `/`.

## Contributing

Contributions to Sourcery are welcomed and encouraged!

It is easy to get involved. Please see the [Contributing guide](CONTRIBUTING.md) for more details.

[A list of contributors is available through GitHub.](https://github.com/krzysztofzablocki/Sourcery/graphs/contributors)

To give clarity of what is expected of our community, Sourcery has adopted the code of conduct defined by the Contributor Covenant. This document is used across many open source communities, and I think it articulates my values well. For more, see the [Code of Conduct](CODE_OF_CONDUCT.md).

## License

Sourcery is available under the MIT license. See [LICENSE](LICENSE) for more information.

## Attributions

This tool is powered by

- [SourceKitten](https://github.com/jpsim/SourceKitten) by [JP Simard](https://github.com/jpsim)
- [Stencil](https://github.com/kylef/Stencil) and few other libs by [Kyle Fuller](https://github.com/kylef)

Thank you! for:

- [Mariusz Ostrowski](http://twitter.com/faktory) for creating the logo.
- [Artsy Eidolon](https://github.com/artsy/eidolon) team, because we use their codebase as a stub data for performance testing the parser.
- [Olivier Halligon](https://github.com/AliSoftware) for showing me his setup scripts for CLI tools which are powering our rakefile.

## Other Libraries / Tools

If you want to generate code for asset related data like .xib, .storyboards etc. use [SwiftGen](https://github.com/AliSoftware/SwiftGen). SwiftGen and Sourcery are complementary tools.

Make sure to check my other libraries and tools, especially:
- [KZPlayground](https://github.com/krzysztofzablocki/KZPlayground) - Powerful playgrounds for Swift and Objective-C
- [KZFileWatchers](https://github.com/krzysztofzablocki/KZFileWatchers) - Daemon for observing local and remote file changes, used for building other developer tools (Sourcery uses it)

You can [follow me on Twitter][1] for news/updates about other projects I am creating.

 [1]: http://twitter.com/merowing_
