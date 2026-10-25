# AshenFlameAssets

AshenFlameFoundry 的**按需下载资源包**仓库。主 APK 不再内置这些大体积素材，用户第一次用到某个功能时由 App 自动下载。

## 为什么单独放

主 APK 原本 305 MB，其中 `assets/` 占 200 MB —— 主要是 OpenCut 视频剪辑器的整站素材（28606 个文件、344 MB 原始体积）。这些素材只有用到「视频剪辑」功能的人才需要，却要所有下载者一起承担。

## 下载源

**直连 `github.com` 在国内基本不可达**，App 端统一走镜像：

| 源 | 地址 |
|---|---|
| gh-proxy（推荐） | `https://gh-proxy.com/<原始 URL>` |
| ghfast | `https://ghfast.top/<原始 URL>` |

原始 URL 形如：

```
https://github.com/151shi23/AshenFlameAssets/releases/download/v1/opencut.zip
```

镜像转发后**支持 Range 请求**（返回 `206`），所以断点续传和多路并行都可用。实测单路约 1.1 MB/s，4 路并行约 4.3 MB/s。

## 资源清单

App 端不硬编码地址，统一读仓库根目录的 [`manifest.json`](manifest.json)：

```json
{
  "schema": 1,
  "packs": [
    {
      "id": "opencut",
      "name": "视频剪辑素材",
      "version": "1.0",
      "size": 113090709,
      "sha256": "199f14e9...",
      "url": "https://github.com/.../opencut.zip",
      "installDir": "opencut"
    }
  ]
}
```

| 字段 | 说明 |
|---|---|
| `size` | 字节数，用于校验下载完整性 |
| `sha256` | 下载后必须校验，不匹配就丢弃重下 |
| `installDir` | 解压到 `filesDir/assets/<installDir>/` |
| `version` | 本地记录的版本低于此值时才提示更新 |

## 包格式

zip，内部顶层目录是 `opencut/`，解压后直接得到 `filesDir/assets/opencut/`，与 APK 内 `assets/opencut/` 结构完全一致 —— 所以 App 侧可以「外部优先、内置回落」无缝切换。

## 新增资源包

1. 按上面的结构压 zip，顶层目录名 = `installDir`
2. 算 `sha256` 和字节数
3. `gh release upload <tag> <file>`
4. 更新 `manifest.json` 并提交

不需要发新 APK —— 资源包与主程序解耦。
