# Vanilo Support Module Changelog

## 4.x Series

## Unreleased
##### 2023-XX-YY

- Dropped PHP 8.0 & PHP 8.1 Support
- Dropped Laravel 9 Support

## 3.x Series

## 3.8.0
##### 2023-05-24

- Bump module version to mainline (no change)

## 3.7.0
##### 2023-04-04

- Added the `fromKeyValuePairs` static factory and `getDetail()` methods to the DetailedAmount Dto class

## 3.6.1
##### 2023-03-09

- Fixed the `ConfigurableModel` trait to handle json strings and other arrayable fields in the underlying model

## 3.6.0
##### 2023-03-07

- Added Laravel 10 support
- Added the `DetailedAmount` DTO class (provides a default implementation for the same name interface)
- Added the `Dimension` DTO class (provides a default implementation for the same name interface)

## 3.5.0
##### 2023-02-23

- Added the `Addresses::are()` comparison utility

## 3.4.0
##### 2023-01-25

- Added the `ConfigurableModel` trait

## 3.3.0
##### 2023-01-05

- No-change version bump to match with the rest of the Vanilo Modules

## 3.2.0
##### 2022-12-08

- No-change version bump to match with the rest of the Vanilo Modules

## 3.1.0
##### 2022-11-07

- Changed minimum Laravel requirement to 9.2
- Changed suggested Concord version to 1.11

## 3.0.1
##### 2022-05-22

- Bump module version to mainline (no change)

## 3.0.0
##### 2022-02-28

- Added Laravel 9 support
- Added PHP 8.1 support
- Dropped PHP 7.4 Support
- Dropped Laravel 6-8 Support
- Removed Admin from "Framework" - it is available as an optional separate package see [vanilo/admin](https://github.com/vanilophp/admin) 
- Minimum Laravel version is 8.22.1. [See GHSA-3p32-j457-pg5x](https://github.com/advisories/GHSA-3p32-j457-pg5x)


---

## 2.x Series

### 2.2.0
##### 2021-09-11

- Changed internal CS ruleset from PSR-2 to PSR-12
- Dropped PHP 7.3 support

### 2.1.1
##### 2021-01-06

- Fixed composer version constraint

### 2.1.0
##### 2020-12-31

- Added PHP 8 support
- Added `HasImagesFromMediaLibrary` trait for supporting the new `HasImages` interface
- Deprecated the `BuyableImageSpatieV8` and `BuyableImageSpatieV7` traits
- Added generic NanoId generator utility class
- Changed CI from Travis to Github

### 2.0.1
##### 2020-10-28

- Improved type safety on `AddressModel` trait

### 2.0.0
##### 2020-10-11

- BC: Changed traits to v2 interfaces
- Added Laravel 8 support
- Dropped Laravel 5 support
- Dropped PHP 7.2 support

## 1.x Series

### 1.2.0
##### 2020-03-29

- Added Laravel 7 support
- Added PHP 7.4 support
- Dropped PHP 7.1 support

### 1.1.0
##### 2019-11-25

- Added Laravel 6 Support

### 1.0.0
##### 2019-11-11

- Dropped PHP 7.0 support

## 0.5 Series

### 0.5.0
##### 2019-02-11

- Version bumped for v0.5

## 0.4 Series

### 0.4.0
##### 2018-11-12

- Added `AddressModel` trait
- BuyableModel trait supports the new addSale/removeSale sale methods

## 0.3 Series

### 0.3.1
##### 2018-08-11

- Added Laravel 5.4 test compatibility trait

### 0.3.0
##### 2018-08-11

- Support for Buyable images

## 0.2 Series

### 0.2.0
##### 2018-02-19

- Bugfixes

## 0.1 Series

### 0.1.0
##### 2017-12-11

- Supports Buyable model

