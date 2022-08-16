# Install SimpleITK-Elastix for python3 in a anaconda virtual environment (Mac)
## 1. Install the prerequisites (try to install if you can)
```
brew install cmake swig monodevelop r-base r-base-dev ruby ruby-dev python python-dev python3 python3-dev tcl tcl-dev tk tk-dev
```

## 2. Build Elastix
```
git clone https://github.com/kaspermarstal/SimpleElastix
cd ~/SimpleElastix
mkdir build
cd build
cmake -DPYTHON_EXECUTABLE:FILEPATH=/usr/bin/python3 \
-DPYTHON_INCLUDE_DIR=$(/usr/bin/python3 -c "from distutils.sysconfig import get_python_inc; print(get_python_inc())")  \
-DPYTHON_LIBRARY=$(/usr/bin/python3 -c "import distutils.sysconfig as sysconfig; print(sysconfig.get_config_var('LIBDIR'))")\
-DBUILD_EXAMPLES=OFF -DBUILD_TESTING=OFF \
-WRAP_CSHARP=OFF -WRAP_JAVA=OFF -WRAP_LUA=OFF \
-WRAP_R=OFF -WRAP_DEFAULT=OFF \
-WRAP_TCL=OFF -WRAP_PYTHON=ON ../SuperBuild
```

## 3. Setting Parameters
Go to the build directory you created -> find the **CMakeCache.txt** file -> open it with a text editor
Search for this line:
```
//Wrap Ruby
WRAP_RUBY:BOOL=ON
```
Replacing the ON with OFF.
Set all the **WRAP_...** to OFF instead of **WRAP_PYTHON:BOOL=ON**.
  
## 4. Make
```
make -j4
```
(or you can simply just enter **make**)
This will take several hours...
The compilation should end with no error,
something like this:
```
[100%] Completed 'SimpleITK'
[100%] Built target SimpleITK
```

## 5. Search for Setup.py file
Head to the **build/SimpleITK-build/Wrapping/Python** and search for the **setup.py** file 
(as for me in the *build/SimpleITK-build/Wrapping/Python/Packaging**)

## 6. Activate your virtual environment and run the installation step
```
conda activate <your virtenv>
python setup.py install
```
## 7. Encounting errors:
(1)If you encounter something like
```
running build_ext
 error: can't copy '/home/user/Code/SimpleElastix/build/SimpleITK-build/Wrapping/Python/Packaging/_SimpleITK.so': doesn't exist or not a regular file
```
Locate the file in the parent folder (in this case **_SimpleITK.so**), copy it to the Packaging folder(where setup.py is) and repeat the installation step.

(2)If you encounter:
```
...
error: [Errno 13] Permission denied
...
```
run
```
sudo chown <user> <file>
```
to own the build folder.
Replace <user> with your user, and replace <file> with the file where the error showed.

After fixing the errors, run  installation```python setup.py install```again.
The installation step should finish with no error.

## 8. Check if the installation succeded, seeing if the Elastix function is available:
```
(pytorch) ksy@guosiyande-MacBook-Air ~ % python
Python 3.7.13 (default, Mar 28 2022, 07:24:34) 
[Clang 12.0.0 ] :: Anaconda, Inc. on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> import SimpleITK
>>> SimpleITK.Elastix
<function Elastix at 0x7f7b7230eb90>
```
Now you can celebrate!
If it appears:
```
>>> sitk.Elastix
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: module 'SimpleITK' has no attribute 'Elastix'
```
you may need to uninstall the previous SimpleITK to use the newer version.
Go to the directory where it said it installed the New SimpleITK file, and delete the other SimpleITK file.
As for me: search for SimpleITK in /Users/ksy/miniconda3/envs/<env name>/lib/python3.7/site-packages





