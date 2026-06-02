#### 1. 文本差异显示相关

```
diff.astextplain.textconv=astextplain
```

- **作用**：告诉 Git 在对比（diff）非文本文件（比如二进制文件、Word 文档）时，调用 `astextplain` 工具将其转换为纯文本，这样你能看到这些文件的文本化差异，而不是一堆乱码。
- **场景**：比如你修改了一个 PDF/Word 文件，执行 `git diff` 时，Git 会尝试把文件转成纯文本再显示差异。

#### 2. Git LFS（大文件存储）相关（处理大文件的核心配置）

```
filter.lfs.clean=git-lfs clean -- %f
filter.lfs.smudge=git-lfs smudge -- %f
filter.lfs.process=git-lfs filter-process
filter.lfs.required=true
```

这 4 行是 Git LFS（Large File Storage）的核心配置，专门解决 Git 默认不擅长管理大文件（比如视频、图片、压缩包）的问题：

- `filter.lfs.clean`：提交（commit）大文件时，Git LFS 会先对文件做 “清理”（比如替换为一个轻量级指针文件），避免把大文件直接存到 Git 仓库里；
- `filter.lfs.smudge`：拉取（pull/clone）仓库时，Git LFS 会把指针文件还原成实际的大文件；
- `filter.lfs.process`：指定 Git LFS 的处理进程，协调上述 “清理 / 还原” 操作；
- `filter.lfs.required=true`：强制要求使用 Git LFS 处理匹配规则的大文件，不允许跳过。
- **场景**：如果你仓库里有超过 100MB 的文件，必须靠 LFS 才能正常提交 / 拉取。

#### 3. HTTPS/SSL 证书相关（保证 Git 访问 HTTPS 仓库的安全性）

```
http.sslbackend=openssl
http.sslcainfo=E:/Git/mingw64/etc/ssl/certs/ca-bundle.crt
```

- `http.sslbackend=openssl`：指定 Git 使用 OpenSSL 库来处理 HTTPS 的 SSL/TLS 加密（OpenSSL 是最常用的加密库）；
- `http.sslcainfo`：指定 SSL 证书的存放路径（这里是你本地 Git 安装目录下的证书文件），Git 通过这个证书验证 GitHub/Gitee 等服务器的合法性，避免 “证书不安全” 的报错。

#### 4. 核心文件系统 / 换行符配置（跨平台关键）

```
core.autocrlf=true          # Windows系统核心配置
core.fscache=true           # 文件系统缓存
core.symlinks=false         # 关闭符号链接
core.filemode=false         # 不检测文件权限变化（Windows无文件权限）
core.ignorecase=true        # 忽略文件名大小写（Windows文件系统不区分大小写）
```

这是适配 Windows 系统的核心配置（Linux/Mac 配置会不同）：

- ```
  core.autocrlf=true
  ```

  ：最关键！Windows 换行符是

  ```
  CRLF(\r\n)
  ```

  ，Linux/Mac 是

  ```
  LF(\n)
  ```

  。设置为

  ```
  true后：
  ```

  

  ✅ 提交文件时，Git 自动把

  ```
  CRLF
  ```
  
  换成

  ```
  LF
  ```
  
  （统一仓库内的换行符）；

  

  ✅ 拉取文件时，Git 自动把

  ```
  LF
  ```
  
  换回

  ```
  CRLF
  ```
  
  （适配 Windows 编辑器）；

- `core.fscache=true`：开启文件系统缓存，提升 Git 操作（比如`git status`）的速度；

- `core.symlinks=false`：Windows 默认不支持 Linux 风格的符号链接，关闭后避免报错；

- `core.filemode=false`：Windows 没有 Linux 的文件执行权限（chmod +x），关闭后 Git 不会把 “权限变化” 当成文件修改；

- `core.ignorecase=true`：Windows 中`README.md`和`readme.md`是同一个文件，Git 忽略大小写差异，避免冲突。

#### 5. 拉取（pull）行为配置

```
pull.rebase=false
```

- 作用

  ：控制

  ```
  git pull
  ```
  的默认行为：

  - `false`（默认）：`git pull = git fetch + git merge`（合并远程分支，会产生 merge 提交）；
  - `true`：`git pull = git fetch + git rebase`（变基，让提交记录更线性）。


- **场景**：新手用默认的`false`即可，避免变基导致的冲突处理复杂度。

#### 6. 凭证（账号密码）管理配置

```
credential.helper=manager
credential.https://dev.azure.com.usehttppath=true
```

- `credential.helper=manager`：指定 Git 使用 “凭证管理器” 保存你的账号密码（比如 GitHub/Gitee 的账号），不用每次拉取 / 推送都输入密码（Windows 下会弹密码框，保存后下次自动用）；
- `credential.https://dev.azure.com.usehttppath=true`：专门适配微软 Azure DevOps 的凭证管理，确保访问 Azure 仓库时凭证路径正确。

#### 7. 仓库基础配置


```
init.defaultbranch=master   # 新建仓库时默认主分支名是master（新版Git可能是main）
core.repositoryformatversion=0  # Git仓库格式版本（固定值，无需修改）
core.bare=false             # 不是“裸仓库”（裸仓库只有代码，没有工作目录，用于服务器）
core.logallrefupdates=true  # 记录所有分支/标签的更新日志（方便追溯操作）
```

- `core.bare=false`：你的本地仓库是 “工作仓库”，有工作目录（能看到代码文件），而服务器上的仓库一般是`core.bare=true`（只有.git 目录，没有实际代码文件）。

### 总结

1. **核心分类**：这些配置覆盖了 Git 的「文本处理」「大文件管理（LFS）」「网络安全（SSL）」「跨平台适配（换行符 / 文件系统）」「凭证管理」「仓库基础规则」六大核心场景；
2. **关键配置**：`core.autocrlf=true`（Windows 换行符适配）、`filter.lfs.*`（大文件处理）、`http.sslcainfo`（HTTPS 证书）是日常使用中最容易出问题的配置；
3. **适配性**：大部分配置是为 Windows 系统定制的（比如`core.symlinks=false`、`core.ignorecase=true`），Linux/Mac 的配置会有差异（比如`core.autocrlf=input`）。

这些配置都是 Git 的默认 / 推荐配置，不用手动修改，除非你有特殊需求（比如禁用 LFS、修改默认分支名）。如果某一项配置出问题（比如 SSL 证书路径错了），才需要针对性调整。