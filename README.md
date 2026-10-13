# OSINT 工具速查表

OSINT（Open Source Intelligence，开源情报）就是只用公开渠道能查到的信息做调查：一个用户名在哪些平台注册过、一个手机号的归属运营商、某个域名藏着多少子域名。做安全的人用它摸清自家资产暴露面，蓝队用它做事件溯源，记者用它核查线索。这份速查表把主流工具按用途分好类，每条给一句用途、一行命令，查的时候不用再满网找。

**先把规矩说在前面**：只查你自己的信息、或你有明确授权的目标。绕开平台的访问控制、拿别人的数据牟利、跟踪骚扰，在大多数法域都是违法的，美国有 CFAA，各平台也有自己的服务条款。工具本身中立，怎么用是人的事——这份表按防御和学习的视角整理。

> 表里的星标数和许可证是我在 2026 年 10 月逐个查 GitHub 仓库核对过的，之后会有浮动，以仓库页面为准。

---

## 先说 GhostTrack

这份表就是因为它起的头。GhostTrack 是印度尼西亚开发者 HunxByts 写的一个 Python 小工具，把三件事打包成一个菜单程序：

| 功能 | 干什么 |
|---|---|
| IP Tracker | 查一个 IP 的归属地、运营商等公开信息 |
| Phone Tracker | 查手机号的国家、运营商等基础信息 |
| Username Tracker | 拿一个用户名去一批社交平台上撞，看哪些注册过 |

安装很简单，Linux 和 Termux 都能跑：

```bash
git clone https://github.com/HunxByts/GhostTrack.git
cd GhostTrack
pip3 install -r requirements.txt
python3 GhostTR.py
```

成绩不错：18,532 星、2,521 次 fork，在同类小工具里算头部。但有两件事得如实说：

- **没有开源许可证**。仓库里没放 LICENSE 文件，法律上等于作者保留全部权利。你可以下载来学习使用，但不要改一改换个名字当成自己的作品再发布。
- **从 2024 年 1 月起没再更新**。它调用的一些查询接口可能已经变了，装上先拿自己的号码和账号试，别指望每个功能都还灵。

所以我的处理是：拿它当入门练手和理解 OSINT 长什么样的样本，真干活换下面这些还在维护的工具。

---

## 按用户名查：这个人在哪些平台出现过

| 工具 | 用途一句话 | 许可证 / 星标 | 安装或用法 |
|---|---|---|---|
| [Sherlock](https://github.com/sherlock-project/sherlock) | 拿用户名扫 400 多个网站看注册情况，这个品类的标杆 | MIT / 94,005 | `pip install sherlock-project` 然后 `sherlock 用户名` |
| [Maigret](https://github.com/soxoj/maigret) | 覆盖站点更多（3000+），还能出报告，查得比 Sherlock 深 | MIT / 38,859 | `pip install maigret` 然后 `maigret 用户名` |

适用场景：入职背调前自查、发现有人冒用你的网名、溯源一个网暴账号的活动范围。

## 按手机号查

| 工具 | 用途一句话 | 许可证 / 星标 | 安装或用法 |
|---|---|---|---|
| [PhoneInfoga](https://github.com/sundowndev/phoneinfoga) | 查号码的格式、国家、运营商，再汇总各搜索引擎的公开足迹 | GPL-3.0 / 18,133 | 下载官方 release 二进制，`phoneinfoga scan -n +区号号码` |

注意：它查的是公开信息和格式校验，不是定位。任何宣称输个号码就能实时定位的服务，基本都是骗局或违法生意，别碰。

## 按邮箱查

| 工具 | 用途一句话 | 许可证 / 星标 | 安装或用法 |
|---|---|---|---|
| [holehe](https://github.com/megadose/holehe) | 拿邮箱去 120 多个网站试，看在哪些平台注册过，不发验证邮件、对方无感 | GPL-3.0 / 15,180 | `pip install holehe` 然后 `holehe 邮箱` |
| [h8mail](https://github.com/khast3x/h8mail) | 查邮箱有没有出现在已公开的泄露库里，可接本地泄露库和 HIBP 类接口 | BSD / 5,338 | `pip install h8mail` 然后 `h8mail -t 邮箱` |

h8mail 最后更新停在 2023 年，接口可能部分失效；查泄露更稳的日常做法是直接上 [Have I Been Pwned](https://haveibeenpwned.com) 官网输自己的邮箱。

## IP、域名和资产测绘

| 工具 | 用途一句话 | 许可证 / 星标 | 安装或用法 |
|---|---|---|---|
| [Subfinder](https://github.com/projectdiscovery/subfinder) | 快速枚举一个域名的全部子域名，资产盘点第一步 | MIT / 14,587 | 官方 release 或 Docker，`subfinder -d example.com` |
| [OWASP Amass](https://github.com/owasp-amass/amass) | 比 Subfinder 更重的资产测绘，能画出域名、IP、证书之间的关系图 | Apache-2.0 / 15,346 | `amass enum -d example.com` |
| [Shodan](https://www.shodan.io) | 联网设备搜索引擎：查某个 IP 开了哪些端口和服务、有没有已知漏洞。它是在线服务，官网注册使用 | 商业服务 / — | 官方 CLI：`pip install shodan`，`shodan host IP地址` |

适用场景：查自己公司的域名有没有野生的测试服务器裸奔、看看家里的路由器有没有把管理端口开到公网上。

## 综合框架：一个工具不够时

| 工具 | 用途一句话 | 许可证 / 星标 | 安装或用法 |
|---|---|---|---|
| [SpiderFoot](https://github.com/smicallef/spiderfoot) | 带网页界面的自动化情报平台，100 多个模块，给目标自动织一张关系网 | MIT / 23,388 | `pip install spiderfoot` 或 Docker，启动后浏览器里操作 |
| [Recon-ng](https://github.com/lanmaster53/recon-ng) | 元老级命令行侦察框架，模块化，适合写脚本批量跑 | GPL-3.0 / 5,988 | `pip install recon-ng` 然后 `recon-ng` 进框架 |
| [OSINT Framework](https://osintframework.com) | 不是工具，是一个在线目录网站：按「我想查什么」分类列出几百个可用资源 | MIT / 12,273 | 直接开网页点分类往下找 |

## 图片元数据

| 工具 | 用途一句话 | 用法 |
|---|---|---|
| [ExifTool](https://exiftool.org) | 读（和改）照片、文档里的元数据：拍摄设备、时间、GPS 坐标。这个领域的事实标准，官网下载 | `exiftool 照片.jpg` |

一张原图如果没清过元数据，发出去等于附带了拍摄地点。记者传图前要清，普通人发图前也值得查一眼。

---

## 反过来：把你自己的暴露面收小

学 OSINT 最好的第一课，是拿这些工具查自己一遍，然后把洞补上：

1. **查用户名**：用 Sherlock 或 Maigret 跑一遍你所有在用的网名。你早忘了的论坛账号，往往还挂着头像、签名和注册年份，全是能拼出你身份的碎片。不用的注销掉，或者改掉能指向本人的信息。
2. **查邮箱泄露**：在 Have I Been Pwned 输一遍常用邮箱。出现在泄露库里的，说明那家的密码已经在外面流通——那个网站的密码立刻换，且别再和别的站共用。
3. **查手机号**：拿 PhoneInfoga 看看你的号码能查出什么。运营商、归属地这些拿不掉，但可以把各平台「通过手机号找到我」的开关关掉，尤其是社交和支付类 App。
4. **发图先清元数据**：`exiftool -all= 照片.jpg` 一行就能抹掉 GPS 和设备信息。养成习惯：发原图之前先处理，或者干脆截图再发（截图不带原图 EXIF）。
5. **关定位**：手机相机的定位标记、社交 App 的位置打卡、各平台的「附近的人」，逐个确认是关着的。你不主动发位置，元数据也可能替你发。
6. **搜你自己**：每隔几个月，用搜索引擎搜自己的名字+城市、名字+电话。数据经纪商（data broker）网站上如果有你的条目，按页面提供的 opt-out 流程逐个删，加州居民还可以援引 CCPA 要求删除。

---

## 关于这份速查表

作者：Guangyi Zhao。内容是我自己逐项核对、自己写的文字；表里提到的每个工具，版权和商标归各自的原作者，请从链接去原仓库支持他们。本速查表自身的文字以 MIT 许可证发布（见仓库 LICENSE 文件），引用和转载注明出处即可。
