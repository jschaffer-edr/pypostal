pypostal
--------
Fork of pypostal able to be installed on Windows


Installing libpostal C library
------------

Before using the Python bindings, you must install the libpostal C library.


For Windows the build procedure currently requires MSys2 and MinGW. This can be downloaded from http://msys2.org. Please follow the instructions on the MSys2 website for installation. **Be sure to use the MSYS2 MinGW 64-Bit shell when installing libpostal, NOT the MSYS 2 MSYS shell, otherwise you will fail to build a .dll**

Please ensure Msys2 is up-to-date by running:
```
pacman -Syu
```

Install the following prerequisites:
```
pacman -S autoconf automake curl git make libtool gcc mingw-w64-x86_64-gcc
```

Ready to build
```
git clone https://github.com/openvenues/libpostal
cd libpostal
cp -rf windows/* ./
./bootstrap.sh
./configure --datadir=[...some dir with a few GB of space...]
```

[For a successful build, you need to remove "multiple definitions".
Comment out these lines in the src/klib/drand48.h file](https://github.com/bytedeco/javacpp-presets/issues/903#issuecomment-720445442):
```
     // unsigned short _rand48_seed [3];
     // unsigned short _rand48_mult [3];
     // unsigned short _rand48_add;
```

Then build the C library
```
make -j4
make install
```

Notes: When setting the datadir, the `C:` drive would be entered as `/c`. The libpostal build script automatically add `libpostal` on the end of the path, so '/c' would become `C:\libpostal\` on Windows.

The compiled .dll will be in the `src/.libs/` directory and should be called `libpostal-1.dll`.

Creating .lib
------------
Building pypostal requires the generation of a .lib file with the `lib.exe` tool provided by [MS Visual Studio Build Tools](https://visualstudio.microsoft.com/downloads/#build-tools-for-visual-studio-2019) and the `libpostal.def` definition file. To build pypostal successfully `libpostal.def` [must first be modified to include extra definitions exposed by the python module](https://github.com/openvenues/pypostal/issues/23#issuecomment-435170866). Update `libpostal.def` to contain the following:

```
EXPORTS
libpostal_address_parser_response_destroy
libpostal_expand_address
libpostal_expand_address_root
libpostal_expansion_array_destroy
libpostal_get_address_parser_default_options
libpostal_get_default_duplicate_options
libpostal_get_default_fuzzy_duplicate_options
libpostal_get_default_fuzzy_duplicate_options_with_languages
libpostal_get_default_options
libpostal_get_duplicate_options_with_languages
libpostal_get_near_dupe_hash_default_options
libpostal_is_floor_duplicate
libpostal_is_house_number_duplicate
libpostal_is_name_duplicate
libpostal_is_name_duplicate_fuzzy
libpostal_is_po_box_duplicate
libpostal_is_postal_code_duplicate
libpostal_is_street_duplicate
libpostal_is_street_duplicate_fuzzy
libpostal_is_toponym_duplicate
libpostal_is_unit_duplicate
libpostal_near_dupe_hashes
libpostal_near_dupe_hashes_languages
libpostal_normalize_string
libpostal_normalize_string_languages
libpostal_normalized_tokens
libpostal_normalized_tokens_languages
libpostal_parse_address
libpostal_parser_print_features
libpostal_place_languages
libpostal_setup
libpostal_setup_datadir
libpostal_setup_language_classifier
libpostal_setup_language_classifier_datadir
libpostal_setup_parser
libpostal_setup_parser_datadir
libpostal_teardown
libpostal_teardown_language_classifier
libpostal_teardown_parser
libpostal_tokenize
```

Finally generate the .lib file (note that `lib.exe` will be in your shell path if executed from the x64 Native Tools Command Prompt for Visual Studio, otherwise it can be found at ```C:\Program Files (x86)\Microsoft Visual Studio **\VC\bin\lib.exe```)

```
lib.exe /def:libpostal.def /out:libpostal.lib /machine:x64
```

Install Python Package
------------
Clone this repo. 

Copy `src/.libs/libpostal-1.dll` from the libpostal c installation to `postal/libpostal.dll` in this repo.

Modify the HOME variable in ```setup.py``` to point to your msys home directory if you followed these instructions, otherwise the parent directory of your cloned libpostal C repo.

```
HOME = "C:\\msys64\\home\\JSchaffer\\"
```

Install pypostal!
```
python setup.py install
```
