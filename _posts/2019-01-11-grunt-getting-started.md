---
layout: post
title: "Grunt 시작하기"
author: Alex
date: 2019-01-11
categories: javascript build
cover:  "/assets/grunt.jpg"
comments: true
---
## Grunt 쉽게 시작하기

### 1. Grunt 란?

Javascript Task Runner 이다. automation, minification, compilation, unit testing, linting과 같은 반복적인 작업을 쉽게 할수 있도록 도와준다.


### 2. Grunt를 사용해야 하는 이유

다양한 플러그인을 제공하면서 작은 노력으로도 자동화를 할수 있다. 플러그인을 제작하고 npm에 게시하는것도 굉장히 쉽게 할수 있다. grunt는 설정에 기반동작하므로 javascript를 할수 있다면 쉽게 접근이 가능하다.


### 3. Grunt 사용방법

Grunt를 사용하려면 `설정 (initConfig), 로드 (loadNpmTasks), 등록 (registerTask)` 이 3가지만 기억하면 쉽게 사용가능하다.

1) Grunt cli 설치,  Grunt 설치

~~~
$npm install -g grunt-cli
$npm install --save-dev grunt
~~~

2) package.json 생성

~~~
$npm init
~~~

3) Gruntfiles.js 파일 생성

~~~ javascript
const sass = require('node-sass');

module.exports = function(grunt) {
    // 1. configuration
    grunt.initConfig({
        // 파일 concat
        concat: {
            js: {
                // js폴더 안에 있는 모든 파일에 적용 시킨다.
                // src: ['js/jquery-1.12.4.js', 'js/rslides.js'. 'js/scripts.js']
                src: ['js/*.js'],
                // 저장될 폴더 설정, build/script 파일안에 등록
                dest: 'build/scripts.js'
            },
            css: {
                src: ['css/*.css'],
                dest: 'build/styles.css'
            }
        },

        // sass complie용 설정
        sass: {
            options: {
                implementation: sass
            },
            build: {
                files: [{
                    src: 'css/sass/styles.scss',
                    dest: 'css/style.css'
                }]
            }
        },

        // 파일 uglify 설정
        uglify: {
            build: {
                files: [{
                    src: 'build/scripts.js',
                    dest: 'build/scripts.js'
                }]
            }
        }
    });


    // 2. load plugins
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-sass');
    grunt.loadNpmTasks('grunt-contrib-uglify');


    // 3. register tasks
    grunt.registerTask('run', function() {
         console.log('running');
    });
    grunt.registerTask('sleep', function() {
         console.log('sleeping');
    });
    grunt.registerTask('all', ['concat', 'run']);

    // js에 설정된 설정으로만 task를 실행한다.
    grunt.registerTask('concat-js', ['concat:js']);
    grunt.registerTask('concat-css', ['concat:css']);

    // scss 컴파일에도 사용가능하다.
    // 파일의 크기를 줄이기 위해 uglify를 진행한다.
};
 ~~~

4) Grunt plugin 설치

~~~
$npm install grunt-contrib-concat --save-dev
$npm install grunt-sass --save-dev
$npm install grunt-contrib-uglify --save-dev
~~~

5) 실행

~~~
$grunt [task 명]
~~~

### 4. Gruntfiles

1) 프로젝트 파일과 함께 commit해야한다.
    - wrapper function
    - project, task configuration
    - grunt플러그인과 task로딩
    - custom task

2) project, task configuration / custom task
    - 대부분의 grunt작업은 grunt.initConfig 메소드에 전달된 객체에 정의된 configration data에 의존한다.
    - 일반적으로 많이 사용되는 concatenation, minification, linting같은 task를 grunt plugin으로 사용할수 있다.
    - 프로젝트에 Grunt 플러그인이 제공하지 않는 task가 필요한 경우 Gruntfile 내부에서 사용자 정의 작업을 정의 할 수 있다.
    - custom task는 Gruntfile에 정의 할 필요가 없습니다. 외부 .js 파일에 정의되어 grunt.loadTasks 메소드를 통해 로드 할 수 있다.

### 참조

- <https://gruntjs.com>



