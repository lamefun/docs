.. _env_vars:

Environment variables
=====================

These are the environment variables used to customize conan.

Most of them can be set in the ``conan.conf`` configuration file (inside your ``<userhome>/.conan`` folder).

CONAN_CMAKE_GENERATOR
---------------------

Conan ``CMake`` helper class is just a convenience to help to translate conan
settings and options into cmake parameters, but you can easily do it yourself, or adapt it.

For some compiler configurations, as ``gcc`` it will use by default the ``Unix Makefiles``
cmake generator. Note that this is not a package settings, building it with makefiles or other
build system, as Ninja, should lead to the same binary if using appropriately the same
underlying compiler settings. So it doesn't make sense to provide a setting or option for this.

So it can be set with the environment variable ``CONAN_CMAKE_GENERATOR``. Just set its value 
to your desired cmake generator (as ``Ninja``).

CONAN_USER_HOME
---------------

**Defaulted to**: Not defined

Allows defining a custom conan cache directory. Can be useful for concurrent builds under different
users in CI, to retrieve and store per-project specific dependencies (useful for deployment, for example).

.. seealso::

    Read more about it in :ref:`custom_cache`

CONAN_LOGGING_LEVEL
-------------------

**Defaulted to**: ``50``

By default conan logging level is only set for critical events. If you want
to show more detailed logging information, set this variable to lower values, as ``10`` to show
debug information.

CONAN_COMPRESSION_LEVEL
-----------------------

**Defaulted to**: ``9``

Conan uses ``tgz`` compression for archives before uploading them to remotes. The default compression
level is good and fast enough for most cases, but users with huge packages might want to change it and
set ``CONAN_COMPRESSION_LEVEL`` environment variable to a lower number, which is able to get slightly
bigger archives but much better compression speed.

CONAN_SYSREQUIRES_SUDO
----------------------

**Defaulted to**: ``True``/``1``

This environment variable controls whether ``sudo`` is used for installing apt, yum, etc. system
packages via ``SystemPackageTool`` helper, typically used in ``system_requirements()``.
By default when the environment variable does not exist, "True" is assumed, and ``sudo`` is 
automatically prefixed in front of package management commands.  If you set this to "False" or "0"
``sudo`` will not be prefixed in front of the comands, however installation or updates of some 
packages may fail due to a lack of privilege, depending on the user account Conan is running under.

CONAN_COLOR_DISPLAY
-------------------

**Defaulted to**: ``True``/``1``

Useful to remove colored output, set it to ``False``/``0`` to remove console output colors.

CONAN_COLOR_DARK
----------------

**Defaulted to**: ``False``/``0``

Set it to ``True``/``1`` to use dark colors in the terminal output, instead of light ones.
Useful for terminal or consoles with light colors as white, so text is rendered in Blue, Black, Magenta,
instead of Yellow, Cyan, White.

CONAN_USER, CONAN_CHANNEL
-------------------------

Environment variables commonly used in ``test_package`` conanfiles, to allow package creation for
different users and channel without modifying the code. They are also the environment variables
that will be checked when using ``self.user`` or ``self.channel`` in ``conanfile.py`` package recipes
in user space, where a user/channel has not been assigned yet (it is assigned when exported in the local cache).

.. seealso::

    Read more about it in :ref:`user_channel`

CONAN_LOGIN_USERNAME, CONAN_LOGIN_USERNAME_{REMOTE_NAME}
--------------------------------------------------------

**Defaulted to**: Not defined

You can define the username for the authentication process using environment variables.
Conan will use a variable **CONAN_LOGIN_USERNAME_{REMOTE_NAME}**, if the variable is not
declared Conan will use the variable **CONAN_LOGIN_USERNAME**, if the variable is not declared either,
Conan will request to the user to input a username.

These variables are useful for unattended executions like CI servers or automated tasks.

If the remote name contains "-" you have to replace it with "_" in the variable name:

For example: For a remote named "conan-center":

.. code-block:: bash

    SET CONAN_LOGIN_USERNAME_CONAN_CENTER=MyUser

CONAN_PASSWORD, CONAN_PASSWORD_{REMOTE_NAME}
--------------------------------------------

**Defaulted to**: Not defined

You can define the authentication password using environment variables.
Conan will use a variable **CONAN_PASSWORD_{REMOTE_NAME}**, if the variable is not
declared Conan will use the variable **CONAN_PASSWORD**, if the variable is not declared either,
Conan will request to the user to input a password.

These variables are useful for unattended executions like CI servers or automated tasks.

If the remote name contains "-" you have to replace it with "_" in the variable name:

For example: For a remote named "conan-center":

.. code-block:: bash

    SET CONAN_PASSWORD_CONAN_CENTER=Mypassword

CONAN_ENV_XXXX_YYYY
-------------------

You can override the default settings (located in your ``~/.conan/profiles/default`` directory) with environment variables.

The ``XXXX`` is the setting name upper-case, and the ``YYYY`` (optional) is the sub-setting name.

**Examples**:

- Override the default compiler:

.. code-block:: bash

    CONAN_ENV_COMPILER = "Visual Studio"

- Override the default compiler version:

.. code-block:: bash

    CONAN_ENV_COMPILER_VERSION = "14"

- Override the architecture:

.. code-block:: bash

    CONAN_ENV_ARCH = "x86"

CONAN_BASH_PATH
---------------

**Defaulted to**: Not defined

Used only in windows to help the :ref:`tools.run_in_windows_bash()<run_in_windows_bash_tool>` function
to locate our Cygwin/MSYS2 bash. Set it with the bash executable path if it's not in the ``PATH`` or you want to use a different one.

.. _conan_make_program:

CONAN_MAKE_PROGRAM
------------------

**Defaulted to**: Not defined

Specify an alternative ``make`` program to use with the :ref:`build helper AutoToolsBuildEnvironment<autotools_reference>` make() method. 

For example:

.. code-block:: bash

    CONAN_MAKE_PROGRAM = ``mingw32-make``.

.. _conan_trace_file:

CONAN_TRACE_FILE
----------------

**Defaulted to**: Not defined

If you want extra logging information about your conan command executions, you can enable it by setting the ``CONAN_TRACE_FILE`` environment variable.
Set it with an absolute path to a file.

.. code-block:: bash

    export CONAN_TRACE_FILE=/tmp/conan_trace.log

When the conan command is executed, some traces will be appended to the specified file. 
Each line contains a JSON object. The ``_action`` field contains the action type, like ``COMMAND`` for command executions, 
``EXCEPTION`` for errors and ``REST_API_CALL`` for HTTP calls to a remote.

The logger will append the traces until the ``CONAN_TRACE_FILE`` variable is unset or pointed to a different file.

.. seealso::

    Read more here: :ref:`logging_and_debugging` 

.. _conan_log_run_to_file:

CONAN_LOG_RUN_TO_FILE
---------------------

**Defaulted to**: ``0``

If set to ``1`` will log every ``self.run("{Some command}")`` command output in a file called ``conan_run.log``.
That file will be located in the current execution directory, so if we call ``self.run`` in the conanfile.py's build method, the file
will be located in the build folder.

In case we execute ``self.run`` in our ``source()`` method, the ``conan_run.log`` will be created in the source directory, but then conan will copy it 
to the ``build`` folder following the regular execution flow. So the ``conan_run.log`` will contain all the logs from your conanfile.py command
executions.

The file can be included in the conan package (for debugging purposes) using the ``package`` method.

.. code-block:: python

        def package(self):
            self.copy(pattern="conan_run.log", dst="", keep_path=False)

CONAN_LOG_RUN_TO_OUTPUT
-----------------------

**Defaulted to**: ``1``

If set to ``0`` conan won't print the command output to the stdout.
Can be used with ``CONAN_LOG_RUN_TO_FILE`` set to ``1`` to log only to file and not printing the output.

.. _conan_print_run_commands:

CONAN_PRINT_RUN_COMMANDS
------------------------

**Defaulted to**: ``0``

If set to ``1``, every ``self.run("{Some command}")`` call will log the executed command {Some command} to the output.

For example: In the `conanfile.py` file:

.. code-block:: python

    self.run("cd %s && %s ./configure" % (self.ZIP_FOLDER_NAME, env_line))

Will print to the output (stout and/or file):

.. code-block:: bash
    ...
    ----Running------
    > cd zlib-1.2.9 && env LIBS="" LDFLAGS=" -m64   $LDFLAGS" CFLAGS="-mstackrealign -fPIC $CFLAGS -m64  -s -DNDEBUG  " CPPFLAGS="$CPPFLAGS -m64  -s -DNDEBUG  " C_INCLUDE_PATH=$C_INCLUDE_PATH: CPLUS_INCLUDE_PATH=$CPLUS_INCLUDE_PATH: ./configure
    -----------------
    ...

CONAN_VERBOSE_TRACEBACK
-----------------------

**Defaulted to**: ``0``

When an error is raised in a recipe or even in the conan code base, if set to ``1`` it will show the complete traceback to ease the debugging.

CMAKE RELATED VARIABLES
-----------------------

There are some conan environment variables that will set the equivalent CMake variable using the :ref:`cmake generator<cmake_generator>` and
the :ref:`CMake build tool<cmake_reference>`:


+-----------------------------------------+------------------------------------------------------------------------------------------------+
| Variable                                | CMake set variable                                                                             |
+=========================================+================================================================================================+
| CONAN_CMAKE_TOOLCHAIN_FILE              | CMAKE_TOOLCHAIN_FILE                                                                           |
+-----------------------------------------+------------------------------------------------------------------------------------------------+
| CONAN_CMAKE_SYSTEM_NAME                 | CMAKE_SYSTEM_NAME                                                                              |
+-----------------------------------------+------------------------------------------------------------------------------------------------+
| CONAN_CMAKE_SYSTEM_VERSION              | CMAKE_SYSTEM_VERSION                                                                           |
+-----------------------------------------+------------------------------------------------------------------------------------------------+
| CONAN_CMAKE_SYSTEM_PROCESSOR            | CMAKE_SYSTEM_PROCESSOR                                                                         |
+-----------------------------------------+------------------------------------------------------------------------------------------------+
| CONAN_CMAKE_FIND_ROOT_PATH              | CMAKE_FIND_ROOT_PATH                                                                           |
+-----------------------------------------+------------------------------------------------------------------------------------------------+
| CONAN_CMAKE_FIND_ROOT_PATH_MODE_PROGRAM | CMAKE_FIND_ROOT_PATH_MODE_PROGRAM                                                              |
+-----------------------------------------+------------------------------------------------------------------------------------------------+
| CONAN_CMAKE_FIND_ROOT_PATH_MODE_LIBRARY | CMAKE_FIND_ROOT_PATH_MODE_LIBRARY                                                              |
+-----------------------------------------+------------------------------------------------------------------------------------------------+
| CONAN_CMAKE_FIND_ROOT_PATH_MODE_INCLUDE | CMAKE_FIND_ROOT_PATH_MODE_INCLUDE                                                              |
+-----------------------------------------+------------------------------------------------------------------------------------------------+

.. seealso::

    See `CMake cross building wiki <http://www.vtk.org/Wiki/CMake_Cross_Compiling>`_

CONAN_CPU_COUNT
---------------

**Defaulted to**: Number of available cores in your machine.

Set the number of cores that the :ref:`tools.cpu_count()<cpu_count>` will return.
Conan recipes can use the cpu_count() tool to build the library using more than one core.

CONAN_USER_HOME_SHORT
---------------------

**Defaulted to**: Not defined

Specify the base folder to be used with the :ref:`short paths<short_paths_reference>` feature. When not specified, the packages
marked as `short_paths` will be stored in the `C:\\.conan` (or the current drive letter).

If set to "None", it will disable the `short_paths` feature in Windows for modern Windows that enable long paths at the system level.

.. note::

    Please note that this only works with Python 3.6 and newer.

CONAN_READ_ONLY_CACHE
---------------------

**Defaulted to**: Not defined

This environment variable if defined, will make the conan cache read-only. This could prevent 
developers to accidentally edit some header of their dependencies while navigating code in their
IDEs.

This variable can also be set in ``conan.conf`` as ``read_only_cache = True`` in the ``[general]``
section.

The packages are made read-only in two points: when a package is built from sources, and when
a package is retrieved from a remote repository. 

The packages are not modified for upload, so users should take that into consideration before 
uploading packages, as they will be read-only and that could have other side-effects.

.. warning::

    It is not recommended to upload packages directly from developers machines with read-only mode as it could lead to insconsistencies.
    For better reproducibility we recommend that packages are created and uploaded by CI machines.
