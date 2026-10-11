教程：https://blog.imxiaohe.com/2026/10/ipcloudflare.html

1、cm-Workers-CheckSocks5-worker.js搭建cf的hello world，并且添加自定义的域名；
2、fork小何的仓库：https://github.com/hezhanleiok/gate ，并且修改相应的参数；


修改 vpngate.py 文件（一处）
在仓库中打开 vpngate.py，点击右上角铅笔图标进入编辑模式。你只需要修改一处：

搜索 `NODES_URL`（大约在代码第 307 行）：

NODES_URL = os.environ.get("NODES_URL", "https://YOUR_GITHUB_USERNAME.github.io/gate/nodes.txt")
把 YOUR_GITHUB_USERNAME 换成你自己的 GitHub 用户名，例如：

NODES_URL = os.environ.get("NODES_URL", "https://hezhanleiok.github.io/gate/nodes.txt")
⚠️ 重要说明：精简版 vpngate.py 中没有 EDT_UUID 和 EDT_DOMAIN 这两个变量。因为这两个参数属于 edgetunnel 本身，在第二步你已经配置好了。你只需要把 nodes.txt 的 URL 填入 edgetunnel 后台，edgetunnel 会自动用它的 UUID 和域名去生成能用的节点，你完全不用担心无法使用！
修改完成后，点击页面底部的 Commit changes 保存。

配置 Secret 密钥与修改工作流文件
为了防止你的检测 Worker 域名公开后被其他人盗用刷爆额度，我们将检测域名保存在 GitHub Secrets（隐藏密钥）中，让工作流自动读取。

1. 添加 GitHub Secret（配置隐藏变量）

进入你 Fork 后的仓库，点击顶部菜单的 Settings（设置）。
在左侧边栏依次点击 Secrets and variables → Actions。
点击右上角的绿色按钮 New repository secret：
Name（名称）：填入 DOMAIN
Secret（内容）：填入你在第一步得到的完整检测 Worker 链接（例如：https://check-sstp.你的用户名.workers.dev/check?sstp=vpn:vpn@）
点击 Add secret 保存。
2. 修改工作流文件（.github/workflows/vpngate.yml）

在仓库中打开 .github/workflows/vpngate.yml，点击右侧铅笔图标进入编辑模式。


第 1 处【必改项 - 核心密钥】：

找到 CHECK_WORKER: 大概在35行，修改为调用刚添加的 Secret：

修改前（明文暴露域名）：

CHECK_WORKER: "https://你的域名/check?sstp=vpn:vpn@"
修改后（通过 Secret 安全读取）：

CHECK_WORKER: ${{ secrets.DOMAIN }}

注意：这个域名是cf中部署cm-checksocks的hello world项目的自定义域名。


第 2 处【可选项 - 日志站点展示】：

这一步的作用仅仅是在 GitHub Actions 运行完成后的控制台日志尾部打印一行提示信息，改不改完全不影响脚本和节点的自动化抓取运行。如果你希望日志里显示的 URL 对应你自己的 GitHub 账号，大概再71行，可以顺手修改：

修改前：

- name: Show site URL
  run: 'echo "site: https://仓库用户名.github.io/gate/ ..."'
修改后：

- name: Show site URL
  run: 'echo "site: https://你的GitHub用户名.github.io/gate/ ..."'
修改完成后，点击页面右上角的 Commit changes 保存。

3、pages或者hello world搭建cmliu-edgetunnel：https://github.com/cmliu/edgetunnel 的项目，把优选的住宅ip链接："https://YOUR_GITHUB_USERNAME.github.io/gate/nodes.txt" 添加进自定义优选IP里。


