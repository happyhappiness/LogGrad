# LogGrad
Quantitative Assessment of Log Quality in Large-Scale Software.

---

### Introduction
*LogGrad* is a light weight log-quality assessment tool, which is based on Clang.
Given a set of projects, *LogGrad* can automatically rank them by log quality.
The basic idea of ranking is that to what extent the given project misses logs.
The missing logs are classified into 3 types, including project-specific logs, intra-domain important logs and inter-domain important logs.

### Usage

##### Compile
- Change directory to Clang tools directory.
```sh
cd path/to/llvm-source-tree/tools/clang/tools
```
- Download source code.
```sh
git clone https://github.com/ZhouyangJia/LogGrad.git
```
- Add *add_subdirectory(LogGrad)* to CMakeList.txt.
```sh
echo "add_subdirectory(LogGrad)" >> CMakeLists.txt
```
- [Recompile Clang](http://llvm.org/docs/CMake.html) and try following command to test.
```sh
ls path/to/llvm-build/Release/bin/clang-loggrad
```

##### Analyze log information
- Generate *compile_commands.json*. More information about [compile_commands.json](http://clang.llvm.org/docs/JSONCompilationDatabase.html).
```sh
cd test/
tar zxvf bftpd-3.2.tar
cd bftpd/
./configure
bear make
```
- Generate *compiled_files.def*, This file has all names of compiled source files.
```sh
../../script/extract_command.pl compile_commands.json
```
- Generate *function_rule_model.out*. Each line includes a 3-tuple, namely function name, called time and logged time.
```sh
cat compiled_files.def | xargs clang-loggrad -log-grade
```
- Rename the output file. Other output files are in script/results.
```sh
mv function_rule_model.out bftpd.out
```
##### Assessment
Each output file represents one project followed by a domain name.
```sh
cd ../../script/
python loggrad.py results/httpd.out httpd results/nginx.out httpd results/lighttpd.out httpd results/mongrel2.out httpd results/mysql.out database results/postgresql.out database results/berkeleydb.out database results/monetdb.out database
```
