# sigmod2021
This repository will be finalized by 21/7

Compile readdiff.cpp: <br>
g++ -std=c++11 -o read readdiff.cpp -lorc -lzstd -lprotobuf -lsnappy -llz4 -lz -lprotoc -lpthread -lparquet <br>

Dependencies: all the libs that are in the above command + msgpack.hpp (https://github.com/msgpack/msgpack-c/tree/cpp_master)

It implements all read operations (full scan, filtered scan, range query, random row look-up) for all the formats that are mentioned in the paper.

Syntax:

Full scan
./read file.diff 0 (scans the 0 (first) column of the adaptively encoded file)
./read file.localdiff 0 (scans the 0 (first) column of the local dictionary encoded file)
./read file.parquet 0 (scans the 0 (first) column of the parquet file)
./read file.orc 0 (scans the 0 (first) column of the ORC file)
./read file.orderedglob 0 (scans the 0 (first) column of the order preserving global dictionary file)
./read file.unorderedglob 0 (scans the 0 (first) column of a global dictionary encoded file)
./read file.mostlyordered 0 (scans the 0 (first) column of a file encoded with MOPs)
./read file.indirect 0 (scans the 0 (first) column of an indirect coded file)
./

Filtered scan
./read file.diff 0 "value" 1 1 (searches "value" in 0 (first column) and returns the count, as for the 2 flags the first enables zone-maps, the second enables diff dict tighter min/max values)
The same command with different file endings (like above) processes the other file formats.

Range
./read file.diff 0 "value" ">" 1 1 (same as above but with the range symbol added before the 2 flags)

Random Access

./read file.diff 0 1000 (returns the value of row 1000)

./join file1.diff file2.diff 0 1 (join the first column of the first file to the 2 column of the 2nd file, both files are encoded with differential dictionaries)
./join file1.local file2.local 0 1 (same as above but with local dictionaries)




Compile join.cpp: 
g++ -std=c++11 -o join join.cpp

./join file1.diff file2.diff 0 1 (joins first col of file1.diff to the 2nd of file2.diff and returns the count)
./join file1.glob file2.glob 0 1 (same as above but with global encoded files)
./join file1.local file2.local 0 1 (same as above but with local encoded files)


global_encoding.py




Write files with Python libs
dependencies: pandas, pyarrow

import global_encoding 
global_encoding.write_parquet(df, "file.parquet", -1) #writes parquet default parquet file
global_encoding.write_parquet(df, "file.parquet", 65535) # each parquet block consists of 64K records
global_encoding.write_global(df,"file.unorderedglob",0,65535) # encodes with global dictionaries
global_encoding.write_global(df,"file.unorderedglob",1,65535) # encodes with order preserving dictionaries
global_encoding.write_indirect(df,"file.unindirect",0,65535) # encodes with global dictionaries and indirect coding
global_encoding.write_indirect(df,"file.indirect",1,65535) # encodes with order preserving dictionaries and indirect coding
global_encoding.mostlyordered(df,"file.mostlyordered",65535,1,0.1,20); # encode with MOP's 1, 0.1 and 20 are the MOP's parameters pitch, lookahead and batch size


Write adaptive dictionaries using fastparquet:
from fastparquet import write
%time write("friends.diff", df, row_group_offsets=65535)
where df is a pandas dataframe and row_group_offsets is the size of a row group.









