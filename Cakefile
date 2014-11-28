{spawn} = require 'child_process'
{ncp} = require 'ncp'
mkdirp = require 'mkdirp'
fs = require 'fs'

file = 'PythonRequestsCodeGenerator.coffee'
identifier = 'com.luckymarmot.PawExtensions.PythonRequestsCodeGenerator'

extensions_dir = "#{ process.env.HOME }/Library/Containers/com.luckymarmot.Paw/Data/Library/Application Support/com.luckymarmot.Paw/Extensions/"
build_root_dir = "build"
build_dir = "#{ build_root_dir }/#{ identifier }"

# compile CoffeeScript
build_coffee = (callback) ->
    coffee = spawn 'coffee', ['-c', '-o', build_dir, file]
    coffee.stderr.on 'data', (data) ->
        process.stderr.write data.toString()
    coffee.stdout.on 'data', (data) ->
        process.stdout.write data.toString()
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

# archive: create a zip archive from the build
archive = (callback) ->
    zip_file = "#{ identifier }.zip"

    # go to build dir
    process.chdir "#{ build_root_dir }/"

    # delete any previous zip
    fs.unlink zip_file

    # zip
    zip = spawn 'zip', ["-r", zip_file, "#{ identifier }/"]
    zip.stderr.on 'data', (data) ->
        process.stderr.write data.toString()
    zip.stdout.on 'data', (data) ->
        process.stdout.write data.toString()
    zip.on 'exit', (code) ->
        if code is 0
            callback?()
        else
            console.error "zip returned with error code: #{ code }"

task 'build', ->
    build()

task 'install', ->
    build () ->
        install()

task 'archive', ->
    build () ->
        archive()

task 'watch', ->
    spawn 'coffee', ['--watch', '--compile', file]
