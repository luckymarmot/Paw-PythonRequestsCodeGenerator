{print} = require 'sys'
{spawn} = require 'child_process'
{ncp} = require 'ncp'
mkdirp = require 'mkdirp'
fs = require 'fs'

file = 'PythonRequestsCodeGenerator.coffee'
identifier = 'com.luckymarmot.PawExtensions.PythonRequestsCodeGenerator'

extensions_dir = "#{ process.env.HOME }/Library/Containers/com.luckymarmot.Paw/Data/Library/Application Support/com.luckymarmot.Paw/Extensions/"
build_dir = "build/#{ identifier }"

# compile CoffeeScript
build_coffee = (callback) ->
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

# copy files to build directory
build_copy = () ->
    fs.writeFileSync "#{ build_dir }/README.md", fs.readFileSync("./README.md")
    fs.writeFileSync "#{ build_dir }/LICENSE", fs.readFileSync("./LICENSE")
    fs.writeFileSync "#{ build_dir }/python.mustache", fs.readFileSync("./python.mustache")
    fs.writeFileSync "#{ build_dir }/mustache.js", fs.readFileSync("./node_modules/mustache/mustache.js")
    fs.writeFileSync "#{ build_dir }/URI.js", fs.readFileSync("./lib/URI.js")

# build: build CoffeeScript and copy files to build directory
build = (callback) ->
    # mkdir build dir
    mkdirp build_dir, (err) ->
        if err
            console.error err
        else
            build_coffee () ->
                build_copy()
                callback?()

# install: copy files to Extensions directory
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
