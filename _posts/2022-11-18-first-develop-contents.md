---
layout: post
title: First Develop Contents
subtitle: Java script
cover-img: /assets/img/path.jpg
thumbnail-img: /assets/img/thumb.png
share-img: /assets/img/path.jpg
tags: [javascript]
---

# Jekkly 테마를 적용하면서 문제가 발생한 부분을 정리합니다.

- build error
  - Gemfile
    - gem install tzinfo-data 선행 진행.
    - Gem File
      ```
      # frozen_string_literal: true

      source "https://rubygems.org"

      # tzinfo 부분 에러가 발생하여 새로 삽입.
      gem 'tzinfo'
      gem 'tzinfo-data', platforms: [:mingw, :mswin, :x64_mingw]

      gemspec


      gem "webrick", "~> 1.7"

      # admin을 추가하기위한 코드. 근데 그냥 vs-code.dev로 개발 진행하는게 훨씬 간편할 듯 싶다.
      # local에서는 설정관련 내용을 정리해서 올려두고 나중에 vs-code.dev로 원하는 내용만 post하는게 나을 것 같다.
      gem 'jekyll-admin', group: :jekyll_plugins
      ```