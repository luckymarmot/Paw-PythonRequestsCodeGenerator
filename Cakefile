{print} = require 'sys'
{spawn} = require 'child_process'
mkdirp = require 'mkdirp'
{ncp} = require 'ncp'

file = 'PythonRequestsCodeGenerator.coffee'
identifier = 'com.luckymarmot.PawExtensions.PythonRequestsCodeGenerator'
extensions_dir = '~/Library/Containers/com.luckymarmot.Paw/Data/Library/Application Support/com.luckymarmot.Paw/Extensions/'

build = () ->
    # mkdir build dir
    build_dir = "build/#{ identifier }"
    mkdirp build_dir

    # compile coffee script
    coffee = spawn 'coffee', ['-c', '-o', build_dir, file]
    coffee.stderr.on 'data', (data) ->
        process.stderr.write data.toString()
    coffee.stdout.on 'data', (data) ->
        print data.toString()
    coffee.on 'exit', (code) ->
        if code is 0
            console.log '>>> Build successful'
        else
            console.log '!!! Build failed'
    
    # copy files
    ncp 'node_modules/mustache/mustache.js', build_dir
    
    return true

install = () ->
    

task 'build', ->
    build()
    
task 'install', ->
    build() and install()

task 'watch', ->
    spawn 'coffee', ['--watch', '--compile', file]
