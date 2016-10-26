# RacketScript

[![Build Status](https://travis-ci.org/vishesh/racketscript.png?branch=master)](https://travis-ci.org/vishesh/racketscript)

RacketScript is an *experimental* lightweight Racket to JavaScript
compiler. The generated code is ES6, which can be translated to ES5
using [Babel](https://babeljs.io/)
or [Traceur](https://github.com/google/traceur-compiler). RacketScript
aims to leverage both JavaScript and Racket's ecosystem, and make
interoperability between them clean and smooth.

RacketScript takes in Racket source files, uses Racket's macro
expander to
produce
[Fully Expanded Programs](https://docs.racket-lang.org/reference/syntax-model.html#%28part._fully-expanded%29),
and then compile these fully expanded programs to
JavaScript. RacketScript doesn't support Racket features which are
expensive, for example proper tail calls and continuations.

## Try RacketScript

You can try RacketScript in your browser
at [RacketScript Playground](http://rapture.twistedplane.com:8080/).

## Installation

Following system packages are required -

- Racket 6.4 or higher
- NodeJS (4.0 or higher) and NPM
- Make

NPM and Racket package installation steps could be done by directly by
executing `make setup` or `raco pkg install` at RacketScript root
directory.

RacketScript will generate Gulpfiles to compile ES6 to ES5 using
Traceur or Babel.  If you wish to run ES6 modules directly, install
Traceur using NPM. Babel is recommended for writing NodeJS programs.

If you wish to hack on RacketScript code or run tests, lints and
coverage tool, run `make setup-extra` form RacketScript root
directory.  See `Makefile` for more information. This will also
install `js-beautify` which produces beautified JavaScript output.

If you do not wish to pollute your root NPM directory, you can set a
custom global location by changing your `npmrc` (eg.  `echo "prefix =
$HOME/.npm-packages" >> ~/.npmrc`. Then add `/prefix/path/above/bin`
to your `PATH`.

### Build

The compiler is written in Typed Racket. To avoid long startups, it is
advised to pre-compile Racket sources to bytecode. This step is not
required if are not making any changes to compiler.

    # From source root
    $ make build

## Basic Usage

RacketScript compiler is named `racks`. 

    $ racks -h # show help
	
To compile a Racket source file:

    # Installs all NPM dependencies and compile file.rkt
	$ racks /path/to/file.rkt
	
The above command will create a output build directory named
`js-build`, copy RacketScript runtime, copy other support files,
install NPM dependencies, compile `file.rkt` and its dependencies.

The compiled ES6 modules typically goto one of following three
folders:

- "modules": The normal Racket files.
- "collects": Racket collects source files.
- "links": Other third party packages.
- "dist": Contains sources compiled to ES6 or bundled JavaScript ready
  for distribution.

Here are few other examples that would come in handy:

	# To skip `npm install` step. Useful when building
	# for second time.
	$ racks -n /path/to/source.rkt
	
	# To beautify assembled modules use `-b`. Make sure
    # `js-beautify` is installed from NPM or your
	# package manager.
	$ racks -b /path/to/source.rkt

    # Override default output directory
    $ racks -d /path/to/output/dir /path/to/source.rkt
	
	# Print JavaScript output to stdout
	$ racks --js --js-beautify /path/to/source.rkt
		
By default tail call optimization is turned off. To enable translation
of self recursive tail calls to loop, pass `--enable-self-tail` flag.

	$ racks --enable-self-tail /path/to/source.rkt

### Traceur

By default RacketScript will use Traceur and produce
`dist/compiled.js`. To execute inside NodeJS, execute `bootstrap.js`
in output directory. For running in browser, include the Traceur
runtime along with `dist/compiled.js`.

A more robust (and less portable) way, is to run the ES6 modules
generated in `modules` directly from Traceur. Goto `modules` output
directory and execute `$ traceur /path/to/source.js`.

### Babel

RacketScript could also use `Babel`. It will compile each assembled ES6
module to ES5, and put it in `dist` directory, persevering original
directory structure. Replace above command with following -

    # Use `--target` or `-t` flag.
    $ racks --target babel /path/to/source.rkt

This will compile each ES6 module generated by RacketScript, and put
in dist with same directory structure.

## Running Tests and Coverage

	# Install build and test dependencies:
	$ make setup-extra
	
	# Run unit tests
	$ make unit-tests
	
	# Run integration tests
	$ make integration-test
	
	# Run all tests
	$ make test
	
To test Racket output against JavaScript output of specific Racket
source file(s), use [fixtures.rkt](test/fixtures.rkt). Run
`./fixtures.rkt -h` inside `$RACKETSCRIPT/tests` directory to see
basic usage.

For running coverage:

	$ make coverage # Consider coverage of all tests
	$ make coverage-unit-test # Just consider unit-test coverage
	
Look in the [Makefile](Makefile) for more targets.
	
## Related Work

- [Whalesong](https://github.com/dyoo/whalesong/)
- [Urlang](https://github.com/soegaard/urlang)
