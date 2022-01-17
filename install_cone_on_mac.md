# Install Cone on Mac (Darwin x64 architecture)

1. First install llvm-13, you can just do it with `brew` :

`brew install llvm`

2. Look at what brew advises for the environment variables, here's what should be added in the
`~/.bash_profile` for llvm :

```
export PATH="/usr/local/opt/llvm/bin:$PATH"
export LDFLAGS="-L/usr/local/opt/llvm/lib -Wl,-rpath,/usr/local/opt/llvm/lib"
export CPPFLAGS="-I/usr/local/opt/llvm/include"
```

3. Now create a directory where you want to work with Cone and put the two relevant code repos (see
below) :

```
mkdir /path/to/Cone
cd /path/to/Cone
git clone https://github.com/jondgoodwin/cone # First repo
cd cone
```

4. Build the Cone compiler aka `conec`

```
cmake . # Don't forget the dot !
make -j2 # The flag -j2 is optional to compile on 2 cores
```

The compiler executable `conec` should now be right there.

5. After the compilation we have to build the `stdio` library :

```
cd src/conestd
gcc -c stdio.c
ar rcs libstdio.a stdio.o
```

6. Now go back to your Cone home directory to clone the conehome repo (this is where we build Cone projects) :

```
cd /path/to/Cone
git clone https://github.com/jondgoodwin/conehome # Second repo
cd conehome
```

7. Put the conec compiler in the bin directory there :

`mv ../cone/conec bin/`

8. Create a library directory and  put the above-compiled stdio library there :

```
mkdir lib
mv ../cone/src/conestd/libstdio.a lib/
```

9. Define the environment variable `CONEHOME` and add the `bin` directory to your path by adding those two lines to your `~/.bashrc` or `~/.bash_profile` (depending on the terminal you use on Mac, one or the other is read, there's alas no rule that all terminals apply, generally it's `~/.bash_profile`) :

```
export PATH="$PATH:/path/to/Cone/conehome/bin"
export CONEHOME="/path/to/Cone/conehome"
```

10. Time to create a projects directory with all advised sub-directories :

`mkdir -p projects/hello/{src,obj,bin}`

go to the `src` directory and edit a Cone file `hello.cone`

`cd projects/hello/src`

11. Save the following Cone code to `hello.cone` :

```
// We need this when we print
import stdio::*

// You can use both the tabs or the curly braces for blocks
fn main():
    print <- "Hello world !\n"
    curlymain()

fn curlymain() {
    print <- "Hello from curly main !\n"
}
```

11. Now build `hello.cone` and put the object file in the `obj` directory of the project :

`conec -o=../obj/ hello.cone`

Gasp in amazement at the compilation time :-) then link it:

`gcc -o ../bin/hello ../obj/hello.o -L$CONEHOME/lib -lstdio`

Run it :

`../bin/hello`


# To update the Cone compiler

```
cd $CONEHOME/../cone
git pull
```

then proceed to step 4., 5., 7. and 8. as described above.

# Updating the Cone build environment

```
cd $CONEHOME
git pull
```



