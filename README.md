# .htaccessでURLをwww有りに統一するクールな書き方

.htaccessに、「wwwあり」「wwwなし」の設定以外にも色々と好みの設定を書いている場合は、新しく別のサイトを作るときに、この.htaccessを使いまわしたいと思うかもしれない。でも、使いまわす時にいちいち.htaccessを開いてドメインを書き変えるの？なにそれ？クールじゃないよね。

というわけで、今回は.htaccessを使ってURLを「wwwあり」に統一する。ただし、.htaccessに自分のドメインを書かないで、っていう方法を紹介しようと思う。

なお、この方法を教えるのは私ではない。クールな外国人だ！！っていうわけで、こういうクールな方法は大体、クールな外国人がクールな英語で解説してるものなんですよ。っていうわけで、ググりました。見つけました。

```
RewriteEngine On
RewriteCond %{HTTP_HOST} !^www\.
RewriteRule ^(.*)$ https://www.%{HTTP_HOST}/$1 [R=301,L]
```
この.htaccessを使えば、例えば　あなたのサイトのアドレスがhttps://example.com だった場合、このアドレスへアクセスすると自動的に https://www.example.com へリダイレクトされます。やったぜ！

もし、wwwなしに統一する場合はこっち。

```
RewriteEngine On
RewriteCond %{HTTP_HOST} ^www\.(.*)$ [NC]
RewriteRule ^(.*)$ https://%1/$1 [R=301,L]
```

## 解説
### RewriteEngine On
Rewrite機能を有効にします。Rewrite機能とは、アクセスのあったURLを、正規表現で書き換えてから処理する機能です。

### RewriteCond
RewriteCondはRewriteRuleを実行するための条件を定義するための記述です。「RewriteCond条件」に合致した場合に、3行目の「RewriteRule」を実行します。というわけで、具体的な「RewriteCond条件」は %{HTTP_HOST} !^`www`\. の部分という事になります。

### %{HTTP_HOST} !^`www`\.
%{HTTP_HOST} ではホスト名を取得しています。次の !^`www`\. では正規表現を使用しています。!は「否定」、^は「行頭」、\は「正規表現の特殊文字をエスケープ」、.は「任意の一文字（ですが、今回は\でエスケープされているので、「任意の一文字」という意味ではなく、「ドット」を意味します）」。つまり「^`www`\.」で、「その文章は`www`.で始まっている」の意味。しかし、最初に否定を意味する!があるので「その文章は`www`.で始まっていない」。つまり「ホスト名が`www`.で始まっていない場合」という意味になります。

### RewriteRule
RewriteCondの条件に当てはまった場合は、RewriteRuleを実行します。具体的な実行内容は`^(.*)$ http://www.%{HTTP_HOST}/$1 [R=301,L]` の部分です。

### `^(.*)$`
これも正規表現です。^は「行頭」。.は「任意の一文字」。`*`は「直前の文字の0回以上の繰り返し」。$は「行末」。また、()で囲むことで、後で$1の様にして、その内容を取得できます。つまり、これは「始まりから終わりまで、どんな文字でもいい」すなわち「全ての文字」を意味します。例えばアクセスされたページが `http://example.com/abc.html` であった場合は `abc.html` の文字のことです。`http://example.com/dir1/dir2/go.php` であった場合は、 `dir1/dir2/go.php` の文字のことです。

### `https://www.%{HTTP_HOST}/$1`
RewriteCondの条件に当てはまった場合の書き換え後のアドレスです。`www.`がついていなかったホスト名に`www.`をつけています。$1には、 `^(.*)$` で取得した文字列、すなわち `abc.html` や `dir1/dir2/go.php` などが入ります。また、もし最初からホスト名に`www.`がついていた場合（`www.`がついているアドレスにアクセスされた場合）は、そもそものRewriteCondの条件に当てはまらないので、このRewriteRuleは実行されません。

### `[R=301,L]`
R=301とは、`www.`がついていないアドレスへ遷移された場合は`www.`つきのアドレスへ「301リダイレクト」することを意味します。Lとは、「RewriteCond」の条件にあてはまった書き換えルールはこの「RewriteRule」で終わり、ということを意味します（.htaccessでは、その気になれば、RewriteRuleを複数書くことが出来ます）。


# .htaccessでindex.htmlやindex.phpをリダイレクトさせるクールな書き方

```
# index.html を省略
RewriteCond %{THE_REQUEST} ^[A-Z]{3,}\s(.*/)?index\.html[\s?] [NC]
RewriteRule ^(.*)index\.html$ /$1 [R=301,L]

# index.php を省略
RewriteCond %{THE_REQUEST} ^[A-Z]{3,}\s(.*/)?index\.php[\s?] [NC]
RewriteRule ^(.*)index\.php$ /$1 [R=301,L]
```

このhtaccessを使えば、URLがどのサイトでも応用ができるので、とってもクールです。

## 解説
### `RewriteCond %{THE_REQUEST} ...`
ユーザーが直接ブラウザに「index.html」などを入力したときだけリダイレクト。内部処理には影響しません。

### `[R=301,L]`
301リダイレクト（恒久的なリダイレクト）を指定。Lはこのルールで処理を終える指示。

