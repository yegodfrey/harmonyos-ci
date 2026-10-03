# harmonyos-ci（HarmonyOS CI 工具链 / CI toolchain）

> ## ⚠️ 退役声明 / Retirement notice
> **linux 镜像工厂已于 2026-10-03 依业主令彻底退役**（windows 通道建立后，ghcr 镜像零消费）。
> 仓库仅保留 **windows 工具链 release 资产**（`windows-toolchain-1.0` 等，见 Releases）与
> **HAP 构建签名消费方模板**（`build.yml` / `sign-and-release.yml` / `strip_signing.py`）。
> 镜像工厂相关件（`docker-image.yml`、`smoke-image.yml`、`docker/` 整目录）已全部删除，
> 下文的镜像 tag 表与 docker build 用法随之移除。
> The Linux image factory was fully retired on 2026-10-03 by owner's order
> (zero consumers of the ghcr images after the Windows channel landed).
> This repo now only keeps the Windows toolchain release assets and the
> HAP build/sign consumer templates; all image-factory files were removed.

## 目录 / Layout

```
harmonyos-ci/
├── .github/workflows/
│   ├── build.yml                    # 消费方模板（历史模板）：构建未签名 HAP + 滚动 nightly Release
│   └── sign-and-release.yml         # 消费方模板：签名 + 版本化 Release
├── .github/scripts/strip_signing.py # 剥离本机签名配置（产出未签名 HAP）
├── docs/CI_Guide.md                 # 完整中文指南（构建 / 签名 / 排障 / 维护）
├── docs/CI_Guide.en.md              # 完整英文指南
└── README.md
```

> ⚠️ 本仓库的 `build.yml` / `sign-and-release.yml` 是**给消费方复制走的模板**，
> 它们构建的是「当前仓库」的 HarmonyOS 工程；在本仓库自身不会触发（本仓库没有应用工程）。
> The two workflow files are **consumer templates** meant to be copied into an app repo.
>
> ⚠️ 两个模板的容器镜像来源**已退役**：`env.CI_IMAGE` 默认值
> `ghcr.io/dalongzhuazi/harmonyos-ci` 为第三方上游存量镜像，
> 本仓库自 2026-10-03 起不再构建/维护任何镜像。
> The container image source of the templates is **retired**: the default
> `CI_IMAGE` points to a pre-existing third-party upstream image that this
> repo no longer builds or maintains since 2026-10-03.

## 复用到你的项目 / Reusing in your project

复制以下三个文件到你的工程（路径保持一致），然后把 workflow 顶部的 `env.CI_IMAGE` 改成你可用的镜像：

| 从本仓库复制 | 作用 |
|---|---|
| [`.github/workflows/build.yml`](.github/workflows/build.yml) | 构建未签名 HAP + 滚动 `nightly` Release |
| [`.github/workflows/sign-and-release.yml`](.github/workflows/sign-and-release.yml) | `v*` tag 时签名并发布版本化 Release（可选） |
| [`.github/scripts/strip_signing.py`](.github/scripts/strip_signing.py) | 剥离本机签名配置（**必需**，否则 CI 找不到本机证书会构建失败） |

```yaml
env:
  CI_IMAGE: ghcr.io/dalongzhuazi/harmonyos-ci:api26r   # ← 按需替换；来源已退役，见顶部声明
```

`strip_signing.py` 会原地删除 `build-profile.json5` 里的 `app.signingConfigs` 与
`products[].signingConfig`（DevEco Studio 本机自动签名写入的绝对路径在容器里不存在），
只改 CI 工作副本，不影响本机签名构建。

详见 [`docs/CI_Guide.md`](docs/CI_Guide.md) / [`docs/CI_Guide.en.md`](docs/CI_Guide.en.md)。

## 许可 / License

MIT

## 2026-10-03 追加退役

消费方模板 build.yml 与 sign-and-release.yml 一并退役：两者均依赖 linux 容器镜像（ghcr.io/*/harmonyos-ci）执行，windows 通道后零消费。本仓现仅承载 windows 工具链 release 资产（windows-toolchain-1.0 等）与本说明。
