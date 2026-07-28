# anynines 自动部署模板

一键将 **代理服务 (VLESS/Trojan/Shadowsocks) + 哪吒监控 Agent** 部署到 [anynines](https://www.anynines.com/) 云平台，自动生成订阅链接并打印节点信息。

## 📁 仓库结构

```
你的仓库/
├── .cfignore                # 排除 README.md 文件
├── .github/
│   └── workflows/
│       └── deploy.yml       # GitHub Actions 工作流（自动部署）
├── app.py                   # 主程序（代理 + 哪吒线程）
├── main.so                  # 编译好的哪吒监控 Agent 模块
├── index.html               # 首页（建议大家用Ai生成独一无二的静态html网页覆盖此文件）
├── manifest.yml             # Cloud Foundry 部署清单
├── requirements.txt         # Python 依赖
├── runtime.txt              # 指定 Python 版本 (3.12.x)
└── README.md                # 不会被上传
```

## 🛠️ 使用步骤

### 第一步：使用模板新建仓库

1. 点击本仓库右上角的 **Use this template** → **Create a new repository**  
2. 仓库名称随意，**务必选择 Private（私有仓库）**，避免泄露配置  
3. 克隆你新建的仓库到本地（可选，也可以直接在网页端修改文件）

### 第二步：配置 GitHub Secrets

为了让 GitHub Actions 能够登录 anynines，需要设置两个仓库变量：

1. 进入仓库 `Settings` → `Secrets and variables` → `Actions`  
2. 点击 `New repository secret`，分别添加：

   - **`CF_USER`** → 你的 anynines 账号邮箱  
   - **`CF_PASS`** → 你的 anynines 密码  

### 第三步：修改 `manifest.yml` 中的变量

打开 `manifest.yml`，找到文件开头的 **第 12~16 行**，根据你的需要修改默认值：

```python
UUID = os.environ.get('UUID', '你的节点UUID')            # 代理和哪吒共用
NEZHA_SERVER = os.environ.get('NEZHA_SERVER', '')      # 哪吒面板地址，格式: nezha.xxx.com:443
NEZHA_KEY = os.environ.get('NEZHA_KEY', '')            # 哪吒通信密钥
SUB_PATH = os.environ.get('SUB_PATH', '你的订阅路径')    # 订阅 token，例如 sub
NAME = os.environ.get('NAME', '节点名称前缀')            # 会显示在订阅链接备注中
```

> 💡 **说明**  
> - 如果不使用哪吒监控，将 `NEZHA_SERVER` 和 `NEZHA_KEY` 保留为空字符串即可。  
> - `SUB_PATH` 是订阅链接的路径，务必修改成一个独一无二的 token。  
> - `NAME` 会与 ISP 信息组合成节点备注，例如 `Anynines-DE-Amazon.com`。  
> - 其他变量（如 `DOMAIN`）由工作流自动注入，**无需手动修改**。

### 第四步：运行工作流并获取节点

1. 进入仓库 `Actions` 标签页  
2. 在左侧找到 **anynines平台** 工作流，点击 `Run workflow` → `Run workflow` 手动触发  
3. 点击刚刚运行的 workflow，展开 `执行部署` 和 `打印节点信息` 步骤查看实时日志  

部署成功后，你会在日志末尾看到类似输出：

```
📡 订阅链接: https://app-xxxx.de.a9sapp.eu/sub
==================== 节点配置 ====================
vless://...
trojan://...
ss://...
==================================================
```

直接复制节点链接，导入到你的代理客户端即可使用。
