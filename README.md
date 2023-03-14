

# Jupyter extensionの学習

参照： https://jupyterlab.readthedocs.io/en/stable/extension/extension_tutorial.html  
尚、jupyterlab_apodのオリジナルREDEME.ｍｄは [こちら](https://github.com/engbJapan/jupyterlab_apod/README_org.md)  

1.gitubに公開でレポジトリを作成しておく  
https://github.com/<自身のGithubで使われるuser識別名>/jupyterlab_apod
```bash
cd
git config --global user.name <登録ユーザ名>
git config --global user.email <登録メールアドレス>
git config --global --list
cookiecutter https://github.com/jupyterlab/extension-cookiecutter-ts
```  
以下要求と応答：  
```
You've downloaded /home/<ユーザ>/.cookiecutters/extension-cookiecutter-ts before. Is it okay to delete and re-download it? [yes]: yes
Select kind:
1 - frontend
2 - server
3 - theme
Choose from 1, 2, 3 [1]:
author_name [My Name]: <githubのユーザ名>
author_email [me@test.com]: <メールアドレス>
labextension_name [myextension]: jupyterlab_apod
python_name [jupyterlab_apod]: 
project_short_description [A JupyterLab extension.]: Show a random NASA Astronomy Picture of the Day in a JupyterLab panel
has_settings [n]:
has_binder [n]: y
test [y]:
repository [https://github.com/github_username/jupyterlab_apod]: https://github.com/<自身のGithubで使われるuser識別名>/jupyterlab_apod
```
```bash
cd jupyterlab_apod
git init
git add .
echo "# jupyterlab_apod" >> README.md
git add README.md
git commit -m "first commit"
git branch -M main
git remote add jupyterlab_apod https://github.com/<自身のGithubで使われるuser識別名>/jupyterlab_apod.git
git commit -m 'Seed apod project from cookiecutter'
git remote add origin https://github.com/<自身のGithubで使われるuser識別名>/jupyterlab_apod.git

pip install develop

pip install -ve .
#上記のコマンドは、拡張機能のフロントエンド部分を JupyterLab にコピーします。変更を行うたびに、このコマンドを再度実行して、変更を JupyterLab にコピーできます。さらに良いことに、このコマンドを使用して、JupyterLab からソース ディレクトリへのシンボリック リンクを作成できます。これは、JupyterLab で変更が自動的に利用可能になることを意味します。

#jupyter labextension develop --overwrite .
jupyter labextension install .


#エラーJupyterLab と拡張機能@jupyterlab/applicationのバージョン不一致
cat <<EOF
An error occured.
ValueError:
"jupyterlab_apod@0.1.0" is not compatible with the current JupyterLab
Conflicting Dependencies:
JupyterLab              Extension      Package
>=3.0.0 <3.1.0          >=3.1.0 <4.0.0 @jupyterlab/application
See the log file for details:  /tmp/jupyterlab-debug-tm05zd8l.log
EOF
```
終わったら、```jupyter lab```で起動してみる  
起動して、拡張機能の一覧には出るが、Abutボタンしかない：  
![image](https://user-images.githubusercontent.com/39077511/224867241-99da1be7-33a9-490b-9c16-70927a97e4ad.png)
## ここで、githubのレポジトリにPUSH
```bash
git push origin main
##Githubのアカウントユーザ、パスワードを入れるも以下のエラー
~/jupyterlab_apod$ git push origin main
Username for 'https://github.com': ＜ユーザ名＞
Password for 'https://engbjapan@github.com':＜ログインパスワード＞
remote: Support for password authentication was removed on August 13, 2021.
remote: Please see https://docs.github.com/en/get-started/getting-started-with-git/about-remote-repositories#cloning-with-https-urls for information on currently recommended modes of authentication.
fatal: Authentication failed for 'https://github.com/engbJapan/jupyterlab_apod.git/'

#fatal: Authentication failed for 'https://github.com/<自身のGithubで使われるuser識別名>/jupyterlab_apod.git
```
#参考サイトで対応：https://qiita.com/devtoku/items/9fd4f135488420a10f4f#%E8%A7%A3%E6%B1%BA%E6%96%B9%E6%B3%95

1. トークンの作成

GitHubへログインしSettingsをクリック

以下のパスから表示

https://github.com/settings/profile

Developer settingsのPersonal access tokensをクリック＝＞tokens(classic)

上記操作後のURL：https://github.com/settings/tokens


![image](https://user-images.githubusercontent.com/39077511/224871635-34e41c1f-5cba-4205-9915-1f32d7974a01.png)


* Noteは任意
  *  Expirationは有効期間で、私は「No Expiration」で無期限にした。あまり良くない事。
  *  対象チェックボックスでは少なくともrepository,workerが無いとPUSHでエラーとなる。
  *  ページ最後の「Create token」ボタンをクリック
* その後表示のページからアクセストークンをコピーボタンでコピー
  * こんな形式```ghp_XXXXXXXXXXXXXXXXXXXXXXXXXXXXX```
  * このコピーは上記ページの一度きりしかできないので、慎重にコピーして安全にバックアップする事。

2. トークンをコピーしてgit/configに設定
```bash
cp ./.git/config ./.git/config.org #backup
echo "コピーしたトークンを貼り付け" > gitaccesstok.txt
cat gitaccesstok.txt ./.git/config | perl -0pe 's/\n/<LF>/g'|perl -pe 's/^([^<]+)<LF>(.+)(\[remote "origin"\])(<LF>.+)(https:\/\/)([^<]+)([<].+)$/$2$3$4$5$1\@$6$7\n/'|perl -pe 's/<LF>/\n/g' > ./.git/config.token
cat ./.git/config.token
#内容の変更確認　[remote "origin"]のurlがhttps:<トークン>@github.com/<自身のGithubで使われるuser識別名>/jupyterlab_apod.git
#となっていること
[core]
        repositoryformatversion = 0
        filemode = true
        bare = false
        logallrefupdates = true
[remote "jupyterlab_apod"]
        url = https://github.com/engbjapan/jupyterlab_apod
        fetch = +refs/heads/*:refs/remotes/jupyterlab_apod/*
[remote "origin"]
        url = https://ghp_XXXXXXXXXXXXXXXXXXXXXXXXXXXXX@github.com/engbjapan/jupyterlab_apod
        fetch = +refs/heads/*:refs/remotes/origin/*

#そうしたら、それを.git/configに上書き
cp ./.git/config.token ./.git/config
```
3. PUSHする。  
```bash
git push origin main
#何も聞かれず終了する


```
<hr>

# 入れてみたい拡張機能：

https://yiskw713.hatenablog.com/entry/jupyterlab-setup#%E6%8B%A1%E5%BC%B5%E6%A9%9F%E8%83%BD%E3%82%92%E8%BF%BD%E5%8A%A0%E3%81%99%E3%82%8B

