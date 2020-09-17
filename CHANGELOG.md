# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

\<TBD\>

## [2.1.0] - 2020-06-16

### Added

- CCv2 Plugin: Support `extensionPacks` array ([docs][ref]). This is especially relevant for the new ["Integration Extension Pack"][pack]

[ref]: https://help.sap.com/viewer/1be46286b36a4aa48205be5a96240672/latest/en-US/ad98c976ab3d433e935b4b5c89303dd5.html
[pack]: https://help.sap.com/viewer/product/SAP_COMMERCE_INTEGRATIONS/latest/en-US

During the execution of the `bootstrapPlatform` task, the plugin resolves extension packs as dependencies as follows:

- `name` / `version` :\
    `de.hybris.platform:${name}:${version}@zip`
- `artifact`:\
    `${artifact}` (as is, without any changes)\
    if `artifact` is configured, `name` and `version` are ignored (as specified in the docs)

The plugin unpacks all resolved dependencies into the project root folder. (the same way as the platform.zip is unpacked).

### Changed

- Changed the default value for `hybris.cleanGlob` to `glob:**hybris/bin/{ext-**,platform**,modules**}` to match the new folder structure.

## [2.0.0] - 2020-02-16

### Added

- Support Gradle 6.x

### Removed

- Dropped support for Gradle 4.x due to changes in the Gradle Plugin API in 6.x

## [1.5.1] - 2019-10-22

### Fixed

- Change the file format of the deployment package checksum file to follow the latest packaging guidelines. ([#15])

[#15]: https://github.com/sap-staging/commerce-gradle-plugin/pull/15

A big "thank you" to [@I048752] for providing the fix!

[@I048752]: https://github.com/I048752

## [1.5.0] - 2019-08-09

### Added

- CCv2 Plugin: Cloud Extension Pack support

If the cloud extension pack is enabled in your `manifest.json` (`"useCloudExtensionPack": true`), the `bootstrapPlatform` task will automatically:

- download and unpack the extension pack (artifact coordinates: `de.hybris.platform:hybris-cloud-extension-pack:<commerce-version-without-patch>.+`) into the folder `cloud-extension-pack`
- patch `localextensions.xml` to load the extensions from the cloud extension pack, if necessary


### Changed

- Install addons defined in `manifest.json` faster


## [1.4.0] - 2019-04-09

### Added

- Task `buildCCV1Package` now declares task outputs - you can now easily (post)process the generated package ([#12])

[#12]: https://github.com/sap-staging/commerce-gradle-plugin/issues/12

## [1.3.3] - 2019-03-29

### Fixed

- Ensure correct ccv1 deployment package name ([#11])

[#11]: https://github.com/sap-staging/commerce-gradle-plugin/issues/11

## [1.3.2] - 2019-03-18

### Fixed

- Fix calling ant targets on the SAP JVM ([#10])

[#10]: https://github.com/sap-staging/commerce-gradle-plugin/issues/10

## [1.3.1] - 2019-03-07

### Fixed

-  CCv2 plugin correctly parses a minimal `manifest.json` ([#9])

[#9]: https://github.com/sap-staging/commerce-gradle-plugin/issues/9

## [1.3.0] - 2019-01-18

### Added

- Gradle 5+ support\
  Check the [kotlin-dsl-example][example] repository on how to write your build with the Kotlin DSL

- New property `sha256Sum` for `SupportPortalDownloadTask` to support checksums available on launchpad.support.sap.com (More details in the [documentation][doc] and the [FAQ][faq])

[doc]: https://github.com/SAP-staging/commerce-gradle-plugin/wiki/Plugin-mpern.sap.commerce.build#mpernsapcommercebuildtaskssupportportaldownload
[faq]: https://github.com/SAP-staging/commerce-gradle-plugin/wiki/FAQ#where-do-i-find-the-sha256sum-value-for-a-sap-commerce-distribution-in-the-sap-support-portal
[example]: https://github.com/sap-commerce-tools/kotlin-dsl-example

## [1.2.2] - 2019-01-17

### Changed

- CCv2 Plugin: you can override the platform version configured in `manifest.json` in the `build.gradle`

### Fixed

- Improve platform version detection

This should take care of [#8]

[#8]: https://github.com/sap-staging/commerce-gradle-plugin/issues/8

## [1.2.1] - 2018-12-14

### Fixed

- Keep case of ant target ([#5])

[#5]: https://github.com/sap-staging/commerce-gradle-plugin/issues/5

## [1.2.0] - 2018-10-26

### Added

- Include Solr customization in a CCv2 package (PR [#3])

A big "thank you" to [@karol-szczecinski-sap] for implementing the feature!

[#3]: https://github.com/sap-staging/commerce-gradle-plugin/pull/3
[@karol-szczecinski-sap]: https://github.com/karol-szczecinski-sap

## [1.1.1] - 2018-09-24

### Fixed

- Change glob pattern to match ant directory in 1808

## [1.1.0] - 2018-08-27

### Added

* CCV2 `manifest.json` support \
  Configure your local build using a `manifest.json` file using the new CCV2 build plugin.\
  Details can be found in the [wiki][ccv2.build]!

[ccv2.build]:https://github.com/SAP-staging/commerce-gradle-plugin/wiki/Plugin-mpern.sap.commerce.build.ccv2

### Changed

* `HybrisAntTask` \
  configure Ant properties consistently via the new methods `antProperty(String key, String value)` and `setAntProperties(Map<String,String>)`

  Using the built-in method `systemProperty` (provided by `JavaExec`) doesn't work for all cases, unfortunately

## [1.0.2] - 2018-08-23

### Fixed

- Fixed ccv1 package generation on Linux (see PR [#1])

Shout out to [@corneleberle] for providing the fix.

[#1]: https://github.com/sap-staging/commerce-gradle-plugin/pull/1
[@corneleberle]: https://github.com/corneleberle

## [1.0.1] - 2018-06-29

### Fixed

- Fix unpack of platform zip on Windows

## [1.0.0] - 2018-06-25

:tada: Initial release :tada:

[Unreleased]: https://github.com/SAP-staging/commerce-gradle-plugin/compare/v2.1.0...HEAD
[2.1.0]: https://github.com/SAP-staging/commerce-gradle-plugin/compare/v2.0.0...v2.1.0
[2.0.0]: https://github.com/SAP-staging/commerce-gradle-plugin/compare/v1.5.1...v2.0.0
[1.5.1]: https://github.com/SAP-staging/commerce-gradle-plugin/compare/v1.5.0...v1.5.1
[1.5.0]: https://github.com/SAP-staging/commerce-gradle-plugin/compare/v1.4.0...v1.5.0
[1.4.0]: https://github.com/SAP-staging/commerce-gradle-plugin/compare/v1.3.3...v1.4.0
[1.3.3]: https://github.com/SAP-staging/commerce-gradle-plugin/compare/v1.3.2...v1.3.3
[1.3.2]: https://github.com/SAP-staging/commerce-gradle-plugin/compare/v1.3.1...v1.3.2
[1.3.1]: https://github.com/SAP-staging/commerce-gradle-plugin/compare/v1.3.0...v1.3.1
[1.3.0]: https://github.com/SAP-staging/commerce-gradle-plugin/compare/v1.2.2...v1.3.0
[1.2.2]: https://github.com/SAP-staging/commerce-gradle-plugin/compare/v1.2.1...v1.2.2
[1.2.1]: https://github.com/SAP-staging/commerce-gradle-plugin/compare/v1.2.0...v1.2.1
[1.2.0]: https://github.com/SAP-staging/commerce-gradle-plugin/compare/v1.1.1...v1.2.0
[1.1.1]: https://github.com/SAP-staging/commerce-gradle-plugin/compare/v1.1.0...v1.1.1
[1.1.0]: https://github.com/SAP-staging/commerce-gradle-plugin/compare/v1.0.2...v1.1.0
[1.0.2]: https://github.com/SAP-staging/commerce-gradle-plugin/compare/v1.0.1...v1.0.2
[1.0.1]: https://github.com/SAP-staging/commerce-gradle-plugin/compare/v1.0.0...v1.0.1
[1.0.0]: https://github.com/SAP-staging/commerce-gradle-plugin/releases/tag/v1.0.0