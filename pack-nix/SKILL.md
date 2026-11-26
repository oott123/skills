---
name: pack-nix
description: 主要是给已有程序添加 nix 打包、添加测试的时候看；修改 nixos 模块的时候也看看
---

# 打包 nix

## nix 包

- 选择社区的热门缓存方案： rust 用 crane , go 用 nixpkgs.buildGoModule
- 提供 overlay 给用户，方便用户直接挂到 overlay 里然后用 pkgs 获得包
- 如果有前后端 embed 的那种，单独用 nix 构建前端，再构建后端的时候拼进去
- 用 nixpkgs unstable
- 测一下跑完整测试要多久，超过 8 分钟则不跑完整测试

## nixos 模块

- systemd 尽量使用 Dynamic User，不要给用户名
- 需要用密钥的文件通过 LoadCredentials 挂上去
- 预留 EnvironmentFile 配置机密环境变量
- 根据场景增加合适的 systemd 加固
- 用 nix 生成配置文件；freeform 就行，不必在 nix 里重新校验
- 不要加 nixos vm test

## 实践

- nix 代码放到 ./nix 里面；flakes.nix 放根目录
- .gitignore 里增加 /result
- 相关说明放到 ./nix/README.md 里，不要放根目录
