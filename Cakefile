{print} = require 'sys'
{spawn} = require 'child_process'
mkdirp = require 'mkdirp'
{ncp} = require 'ncp'

file = 'PythonRequestsCodeGenerator.coffee'
identifier = 'com.luckymarmot.PawExtensions.PythonRequestsCodeGenerator'

extensions_dir = "#{ process.env.HOME }/Library/Containers/com.luckymarmot.Paw/Data/Library/Application Support/com.luckymarmot.Paw/Extensions/"
build_dir = "build/#{ identifier }"

build_coffee = (callback) ->
    # compile coffee script
    coffee = spawn 'coffee', ['-c', '-o', build_dir, file]
    coffee.stderr.on 'data', (data) ->
        process.stderr.write data.toString()
    coffee.stdout.on 'data', (data) ->
        print data.toString()
    coffee.on 'exit', (code) ->
        if code is 0
            callback?()
        else
            console.error "Build failed with error: #{ code }"

build_copy = (callback) ->
    # copy files to build/ directory
    ncp 'node_modules/mustache/mustache.js', build_dir, (err) ->
        if err
            console.error err
        else
            callback?()

build = (callback) ->
    # mkdir build dir
    mkdirp build_dir, (err) ->
        if err
            console.error err
        else
            build_coffee () ->
                build_copy () ->
                    callback?()

install = (callback) ->
    ncp build_dir, "#{ extensions_dir }/#{ identifier }", (err) ->
        if err
            console.error err
        else
            callback?()

task 'build', ->
    build()

task 'install', ->
    build () ->
        install()

task 'watch', ->
    spawn 'coffee', ['--watch', '--compile', file]
