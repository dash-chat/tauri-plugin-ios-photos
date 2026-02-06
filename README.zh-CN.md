# Tauri Plugin iOS Photos

一个用于访问和管理 **iOS 照片应用中的相册和资源** 的 Tauri 插件，基于原生的 Photos 框架。

该插件允许 Tauri 应用在 iOS 设备上请求照片权限、读取相册、访问照片以及执行基本的相册/照片管理。

> ⚠️ 仅支持 iOS。此插件依赖于 Apple Photos API，在其他平台上不可用。

---

## 功能

- 请求和检查照片库授权
- 列出用户相册
- 从相册中访问照片
- 筛选相册
- 检查相册权限
- 创建和删除相册
- 创建、访问和删除照片
- 创建、访问和删除视频

---

## 要求

- Tauri 2.x (或兼容版本)
- Xcode 及 iOS SDK
- 在 `Info.plist` 中配置正确的照片库权限

---

## iOS 权限

此插件需要访问用户的照片库。

请确保将以下键值添加到您的 **iOS `Info.plist`** 文件中：

```xml
<key>NSPhotoLibraryUsageDescription</key>
<string>允许访问您的照片库</string>

<key>NSPhotoLibraryAddUsageDescription</key>
<string>允许将照片保存到您的照片库</string>
```

---

## 安装

```bash
pnpm add @gbyte/tauri-plugin-ios-photos
# 或
npm install @gbyte/tauri-plugin-ios-photos
# 或
yarn add @gbyte/tauri-plugin-ios-photos
```

将插件添加到您 Tauri 项目的 `Cargo.toml` 中：

```toml
[dependencies]
tauri-plugin-ios-photos = "0.3"
```

或者使用 `cargo add tauri-plugin-ios-photos`。

在您的 `capabilities/default.json` 中配置插件权限：

```json
{
  "permissions": ["ios-photos:default"]
}
```

在您的 Tauri 应用中注册插件：

```rust
fn main() {
    tauri::Builder::default()
        .plugin(tauri_plugin_ios_photos::init())
        .run(tauri::generate_context!())
        .expect("error while running tauri application");
}
```

---

## 使用示例 (概念性)

```ts
import {
  requestPhotosAuth,
  PhotosAuthorizationStatus,
  requestAlbums,
  PHAssetCollectionType,
  PHAssetCollectionSubtype
} from '@gbyte/tauri-plugin-ios-photos'

let photoAuth = ''
let albums = []

requestPhotosAuth()
  .then((status) => {
    switch (status) {
      case PhotosAuthorizationStatus.authorized:
        photoAuth = 'authorized'
        break
      case PhotosAuthorizationStatus.denied:
        photoAuth = 'denied'
        break
      case PhotosAuthorizationStatus.limited:
        photoAuth = 'limited'
        break
      case PhotosAuthorizationStatus.restricted:
        photoAuth = 'restricted'
        break
      case PhotosAuthorizationStatus.notDetermined:
        photoAuth = 'notDetermined'
        break
    }
  })
  .then(() => {
    requestAlbums({
      with: PHAssetCollectionType.smartAlbum,
      subtype: PHAssetCollectionSubtype.albumRegular
    }).then((value) => {
      albums = value
    })
  })
```

---

## API

### 方法

#### `requestPhotosAuth(): Promise<PhotosAuthorizationStatus | null>`

请求照片库授权。

#### `getPhotosAuthStatus(): Promise<PhotosAuthorizationStatus | null>`

获取当前的照片库授权状态。

#### `requestAlbums(payload: RequestAlbumRequest): Promise<AlbumItem[]>`

请求用户设备的相册。

#### `requestAlbumMedias(payload: RequestAlbumMediasRequest): Promise<MediaItem[]>`

根据相册请求用户的媒体文件。

#### `checkAlbumCanOperation(payload: CheckAlbumCanOperationRequest): Promise<boolean>`

检查相册是否支持某项操作。

#### `createAlbum(payload: CreateAlbumRequest): Promise<Identifier | null>`

创建相册。

#### `createPhotos(payload: CreateMediaRequest): Promise<Identifiers | null>`

向相册中创建照片。

#### `createVideos(payload: CreateMediaRequest): Promise<Identifiers | null>`

向相册中创建视频。

#### `deleteAlbum(payload: DeleteAlbumRequest): Promise<boolean>`

永久删除相册。

#### `deleteAlbumMedias(payload: DeleteAlbumMediasRequest): Promise<boolean>`

从相册中永久删除媒体文件。

#### `removeAlbumMedias(payload: DeleteAlbumMediasRequest): Promise<boolean>`

从相册中移除媒体文件（非永久）。

### 类型定义

#### `PhotosAuthorizationStatus`

```ts
/**
 * 有关您的应用访问用户照片库的授权信息。
 *
 * @see [PHAuthorizationStatus](https://developer.apple.com/documentation/photos/phauthorizationstatus)
 */
export declare const PhotosAuthorizationStatus: {
  readonly notDetermined: 0
  readonly restricted: 1
  readonly denied: 2
  readonly authorized: 3
  readonly limited: 4
}
```

#### `PHCollectionEditOperation`

```ts
/**
 * 标识集合可以支持的可能操作的值。
 *
 * @see [PHCollectionEditOperation](https://developer.apple.com/documentation/photos/phcollectioneditoperation)
 */
export declare const PHCollectionEditOperation: {
  readonly deleteContent: 1
  readonly removeContent: 2
  readonly addContent: 3
  readonly createContent: 4
  readonly rearrangeContent: 5
  readonly delete: 6
  readonly rename: 7
}
```

#### `PHAssetCollectionType`

```ts
/**
 * 资产集合类型之间的主要区别。
 *
 * @see [PHAssetCollectionType](https://developer.apple.com/documentation/photos/phassetcollectiontype)
 */
export declare const PHAssetCollectionType: {
  /**
   * 照片应用中的一个相册。
   */
  readonly album: 1
  /**
   * 内容动态更新的智能相册。
   */
  readonly smartAlbum: 2
}
```

#### `PHAssetCollectionSubtype`

```ts
/**
 * 资产集合类型之间的次要区别。
 *
 * @see [PHAssetCollectionSubtype](https://developer.apple.com/documentation/photos/phassetcollectionsubtype)
 */
export declare const PHAssetCollectionSubtype: {
  albumRegular: 2
  albumSyncedEvent: 3
  albumSyncedFaces: 4
  albumSyncedAlbum: 5
  albumImported: 6
  albumMyPhotoStream: 100
  albumCloudShared: 101
  smartAlbumGeneric: 200
  smartAlbumPanoramas: 201
  smartAlbumVideos: 202
  smartAlbumFavorites: 203
  smartAlbumTimelapses: 204
  smartAlbumAllHidden: 205
  smartAlbumRecentlyAdded: 206
  smartAlbumBursts: 207
  smartAlbumSlomoVideos: 208
  smartAlbumUserLibrary: 209
  smartAlbumSelfPortraits: 210
  smartAlbumScreenshots: 211
  smartAlbumDepthEffect: 212
  smartAlbumLivePhotos: 213
  smartAlbumAnimated: 214
  smartAlbumLongExposures: 215
  smartAlbumUnableToUpload: 216
  smartAlbumRAW: 217
  smartAlbumCinematic: 218
  smartAlbumSpatial: 219
  any: -1
}
```

#### `AlbumItem`

```ts
export type AlbumItem = {
  id: string
  name: string
}
```

#### `MediaItem`

```ts
export type MediaItem = {
  id: string
  mediaType: number
  createAt: number
  data?: string
}
```

#### `RequestAlbumRequest`

```ts
export type RequestAlbumRequest = {
  with: PHAssetCollectionType
  subtype: PHAssetCollectionSubtype
}
```

#### `RequestAlbumMediasRequest`

```ts
export type RequestAlbumMediasRequest = {
  id: string
  height: number
  width: number
  quality: number
}
```

#### `CheckAlbumCanOperationRequest`

```ts
export type CheckAlbumCanOperationRequest = {
  id: string
  operation: PHCollectionEditOperation
}
```

#### `CreateAlbumRequest`

```ts
export type CreateAlbumRequest = {
  title: string
}
```

#### `CreateMediaRequest`

```ts
export type CreateMediaRequest = {
  album: string
  files: string[]
}
```

### `Identifier`

```ts
export type Identifier = string
```

#### `Identifiers`

```ts
export type Identifiers = Identifier[]
```

#### `DeleteAlbumRequest`

```ts
export type DeleteAlbumRequest = {
  identifiers: Identifiers
}
```

#### `DeleteAlbumMediasRequest`

```ts
export type DeleteAlbumMediasRequest = {
  album: string
  identifiers: Identifiers
}
```

#### `PluginReturnValue`

```ts
export type PluginReturnValue<T> = {
  value?: T
}
```

---

## 关于图片路径访问

此插件返回的图片路径是本地文件路径，通常指向 iOS 沙盒内部位置。

在 Tauri (尤其是在 iOS / WebView 环境) 中，由于 WebView 的安全和沙盒限制，这些本地路径不能被前端直接访问 (例如通过 `file://` 或原始文件系统路径)。

在前端使用这些图片之前，您必须通过 Tauri 自定义协议 (URI scheme) 将它们暴露出来，以便可以像普通 URL 一样访问。

推荐方法：

1. 在 Tauri 中注册一个自定义 URI scheme (例如 `temp://`)

2. 当前端请求 `temp://<local-path>` 时：

- Tauri 读取相应的本地文件

- 返回带有正确 MIME 类型的二进制数据

1. 前端随后可以正常使用该 URL (例如 `<img src="temp://..." />`)

> ⚠️ 注意：
>
> - 此插件不会自动将本地路径转换为可访问的 URL
>
> - 实现自定义协议是应用程序的责任
>
> - 出于安全原因，建议限制可访问的路径范围

---

## 授权状态

可能的授权状态：

- `notDetermined` (未决定)
- `restricted` (受限制)
- `denied` (已拒绝)
- `authorized` (已授权)
- `limited` (部分授权)

该插件提供了查询和响应这些状态的 API。

---

## 待办事项 / 路线图

- [x] 请求访问授权
- [x] 检查授权类型
- [x] 列出用户设备相册
- [x] 从相册获取照片
- [x] 筛选相册
- [x] 检查相册权限
- [x] 访问照片
- [x] 创建相册
- [x] 创建照片
- [x] 移除相册
- [x] 移除照片
- [x] 删除照片
- [x] 文档改进
- [ ] 示例项目
- [ ] 错误处理标准化

---

## 注意事项

- 所有照片操作都遵循 iOS 的隐私规则。
- 如果权限受限，某些操作可能会静默失败。
- 相册和照片的标识符由系统生成。

---

## 许可证

MIT
