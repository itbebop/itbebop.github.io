---
title: "[Github blog]로컬 환경 구축"
excerpt: "github에 반영 전 로컬에서 변경 사항 확인할 수 있는 환경 세팅"

categories:
  - gitblog
tags:
  - [github, Gitblog]


toc: true
toc_sticky: true

date: 2024-06-05
last_modified_at: 2024-06-05

published: true
---


# 1. ruby install/upgrade

ruby는 깔려있고 jekyll은 없음
- jekyll 설치
  ```
  > gem install jekyll
  ```
  ruby 버전이 낮아서 jekyll이 설치 안됨
![image](https://github.com/itbebop/itbebop.github.io/assets/86880025/9b3b8356-3b70-4398-8216-706d94c2c261)


## 1) ruby upgrade
### Windows OS

- 기존 ruby 삭제하고 상위 버전의 ruby 설치
- ruby install for window https://rubyinstaller.org/downloads/

### Mac OS

- rbenv를 설치하여 ruby 버전 변경

```
> brew install rbenv
```
```
> rbenv install -l
3.1.6
3.2.4
3.3.2
jruby-9.4.7.0
mruby-3.3.0
picoruby-3.0.0
truffleruby-24.0.1
truffleruby+graalvm-24.0.1

Only latest stable releases for each Ruby implementation are shown.
Use `rbenv install --list-all to show all local versions.

```
```
> rbenv install 3.3.2

```
- 설치가 완료된 뒤, rbenv versions를 통해 현재 기기에 설치된 Ruby들의 버전을 확인할 수 있음

```
> rbenv versions

```
- 아래 명령을 통해 rbenv에 설치된 ruby를 바라보도록 변경
```
> rbenv global 3.3.2
```

### rbenv global 명령을 실행 후에도 ruby 버전이 바뀌지 않는 경우
rbenv global 명령을 실행해도 rbenv versions를 통해 확인한 버전과, ruby --version으로 확인한 버전이 다른 경우
```
> rbenv versions
* system
  2.7.5

> rbenv global 2.7.5

> rbenv versions
  system
* 2.7.5 (set by /Users/hangyeongsu/.rbenv/version)

> ruby --version
ruby 2.6.3p62 (2019-04-16 revision 67580) [universal.x86_64-darwin20]

> which ruby
/usr/bin/ruby
```

이 경우 환경변수 설정이 필요
rbenv init을 실행하고 출력되는 eval~ 줄을 2번째 줄에 표시되는 파일 뒤에 붙여넣기
```
> rbenv init
# Load rbenv automatically by appending
# the following to ~/.zshrc:

eval "$(rbenv init - zsh)"
```

vscode로 zshrc 파일 열어서 붙여넣음
```
> code ~/.zshrc
```

터미널을 재실행하면 ruby 버전이 잘 적용됨
```
> rbenv versions
  system
* 2.7.5 (set by /Users/hangyeongsu/.rbenv/version)

> ruby --version
ruby 2.7.5p203 (2021-11-24 revision f69aeb8314) [x86_64-darwin20]

> which ruby
/Users/user/.rbenv/shims/ruby
```

# 2.  jekyll 설치
```
> gem install jekyll
```

# 3. bundle 설치 및 Server 실행

## 1) github.io 폴더로 이동하여 아래 명령어 실행
```
> bundle install
```

## 2) 사이트 및 로컬 서버 실행
```
> bundle exec jekyll serve
```
### 에러 발생
```
  Please add the following to your Gemfile to avoid polling for changes:
    gem 'wdm', '>= 0.1.0' if Gem.win_platform?
 Auto-regeneration: enabled for 'D:/source/itbebop.github.io'
                    ------------------------------------------------
      Jekyll 4.3.3   Please append `--trace` to the `serve` command
                     for any additional information or backtrace.
                    ------------------------------------------------
C:/Ruby33-x64/lib/ruby/3.3.0/bundled_gems.rb:74:in `require': cannot load such file -- rack/handler (LoadError)
```
### gemfile 수정 방법
```
# 프로젝트 루트 폴더에 있는 Gemfile을 직접 수정하거나
# 프로젝트 터미널에서 
> open -e Gemfile
```
#### 최초 gemfile  
```
source "https://rubygems.org"
gemspec

gem 'jekyll-admin', group: :jekyll_plugins
gem 'jekyll-sitemap'
```

### 해결
Gemfile에 gem "rackup"을 추가하고
bundle 인스톨

```
source "https://rubygems.org"
gemspec

gem 'jekyll-admin', group: :jekyll_plugins
gem 'jekyll-sitemap'
gem "rack", "~> 3.0"
gem "rackup"
```
```
bundle install
```
### 참고
https://github.com/jekyll/jekyll-admin/issues/705