# huffman
Learning Huffman coding

# Installation

```sh
mkdir build && cd build
cmake .. -DCMAKE_CXX_COMPILER=/usr/bin/g++ && make && make install
```

# Documentation

This project uses doxygen for documentation

```sh
sudo apt install -y doxygen
doxygen ./Doxyfile
xdg-open ./doc/html/index.html
```

This will create the documentation in `/doc`.

# Testing

This project uses the googletest suite for unit testing

```sh
sudo apt install -y libgtest-dev
cd build
cmake .. -DUNIT_TESTING=ON
```
