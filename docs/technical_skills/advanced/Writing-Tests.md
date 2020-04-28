# Writing Python Tests

## Who this is for

- Lab members who are already a bit comfortable with programming and want to improve their skills
- Those who want to contribute code to our Datman or Dashboard github repositories

This page describes how to write unit tests, with code examples written in python 2.7.

## Contents

- [Writing Python Tests](#writing-python-tests)
  - [Who this is for](#who-this-is-for)
  - [Contents](#contents)
  - [Setting Up](#setting-up)
    - [Nose](#nose)
    - [Mock](#mock)
  - [The Basics](#the-basics)
    - [Basic Test Structure](#basic-test-structure)
    - [Edge Cases and Choosing the Input](#edge-cases-and-choosing-the-input)
    - [Writing Test Classes versus Test Functions](#writing-test-classes-versus-test-functions)
    - [Set Up and Tear Down](#set-up-and-tear-down)
    - [Test Fixtures](#test-fixtures)
  - [Mocks](#mocks)
    - [Mocks and Dependency Injection](#mocks-and-dependency-injection)
    - [Patches](#patches)
      - [Patch Decorator Pitfalls](#patch-decorator-pitfalls)
    - [Mocks that Return Something](#mocks-that-return-something)
    - [Mock 'spec'](#mock-spec)
  - [Good Practices](#good-practices)

## Setting Up

It's good practice to store your tests close to your code, but separated into a 'tests' folder. Where you put this folder in your project is up to you though. In addition, you may want to install a library for mocks and a library for automated test discovery before you begin. In our lab, for Python 2.7, we use 'mock' and 'nose'. Nose is also available for Python 3 and 'mock' is built into unittest for Py3 (i.e. use unittest.mock instead of downloading an extra package).

### Nose

[Nose](http://nose.readthedocs.io/en/latest/usage.html) is a handy package (available in both Python2 and Python3) that will discover all of your tests, run them, and print a little report about the results. If you want to use it to help you test (we do for datman and the dashboard) there are some naming rules you should follow:

  1. Name your test scripts either starting or ending with 'test'. e.g. 'test_myscript.py' or 'myscript_test.py'.
  2. Name any test cases within the script either or starting or ending with 'test'. Preferably with 'test' at the start of the name, since this is what python's built in unittest library looks for.

These rules are also used by other automated test discovery packages (like pytest) so it's a good naming practice to use regardless of which tools you end up using. Nose will use these two hints to locate scripts that contain tests (in case you mix some non-test scripts in the same directory) and to tell the difference between tests and support functions/methods stored in these scripts.

There are a few ways to run tests with nose, shown below. Examples assume your current working directory is the one that contains your test folder. Otherwise, provide the path to the tests folder.

From the command line:

```shell
# Run all tests in the 'tests' folder
nosetests tests

# Run all tests in one test script
nosetests tests/test_myscript.py


# Run a single test

# If your test is a function
nosetests tests/test_myscript.py:test_function_name

# If your test is a method
nosetests tests/test_myscript.py:TestClass.test_method_name
```

### Mock

Mock ([Python 2 docs](http://www.voidspace.org.uk/python/mock/), [Python 3 docs](https://docs.python.org/3/library/unittest.mock.html)) and other packages like it allow you to test without having to worry about what the surrounding environment looks like. By this I mean that if your code tries to read something from a file stored somewhere, connect to the internet, communicate with a database, or interact with anything else from outside of your code, a mock can help hide this and make testing easier by ensuring your tests don't require huge amounts of setup and don't fail for any reason other than something going wrong with your code.

The downsides to mocks are that they can be difficult to understand conceptually, can make tests more complex / harder to read, and if you're not careful when you set them up your tests may not actually be testing what you think. Despite this, though, they make otherwise untestable code testable and are therefore worth the effort to use and learn.

## The Basics

When writing tests you should aim to show that your code is doing _what_ you expect it to do and not testing _how_ it is doing what it's doing. This means that ideally you should write your tests in a way that treats the function as a black box, caring only about inputs and outputs. If you learn to do this successfully, you should usually be able to write some tests before you've even written the code you're testing (this is how [test driven development works](https://en.wikipedia.org/wiki/Test-driven_development#Test-driven_development_cycle)).

To use a simple example to illustrate this principle, imagine you intended to write a function called 'alphabet_sort' to sort a list of strings alphabetically. You expect your function to take a list of strings as input and return a sorted list of those same strings as output. Based on this, without ever having written any code for the function itself, you could write the following test:

```python
import mypackage

def test_sort_function():
    # An unsorted test input
    input = ["zoo", "carrot", "apple", "frog"]

    # The list you would expect to get for that input if the function works
    expected = ["apple", "carrot", "frog", "zoo"]

    # Call the function you want to test with your test input
    result = mypackage.alphabet_sort(input)

    # Check that the result is what you expect using an assertion
    assert result == expected
```

### Basic Test Structure

The simplest functions to test are the ones like 'alphabet_sort' from the example above: they take an input, return an output and are completely self contained. For these types of functions your tests will all have a similar format to the above example:

- Create an input to test your function against
- Create the expected output you would get if your function is working
- Call the function to get the real result
- Use an 'assert' statement to compare the real result against your expected result.

Note that you can use your assert statement to assert that your result is NOT like some known bad case or anything else really. Assert only requires a boolean expression and will fail when what it is given evaluates to 'False'. In addition to using a plain assert statement unittest has some special convenience assert methods that you can read more about [here for Python 2.7](https://docs.python.org/2/library/unittest.html#classes-and-functions) or [here for Python 3](https://docs.python.org/3/library/unittest.html#classes-and-functions). In most cases they just wrap a normal assert statement and give a slightly more descriptive message when the assert fails.

Also note that it's usually best to use only one assert statement per test so that if a test fails you immediately know why instead of having to debug your test itself to find the cause. There are some exceptions to this though, for example in some cases you may want to make an assertion about the environment your code will run in to ensure the environment matches what the test expects and to prevent any false failures or passes. The test below gives an example of this kind of assertion: it expects a file to have been created by the function but to ensure it doesn't pass for the wrong reasons it first asserts that the file doesn't exist.

```python
def test_generate_settings_creates_settings_file():
    expected_output = "/tmp/test_settings.yml"

    # Check that the output doesn't already exist to prevent the test
    # passing for the wrong reasons
    assert not os.path.exists(expected_output), "Output already exists"

    actual_output = mypackage.generate_settings()

    assert actual_output == expected_output
```

### Edge Cases and Choosing the Input

A lot of the skill in testing is figuring out what to use as the input and how many inputs to test. This is where edge cases come in. Figure out the 'range' of inputs your function might have to deal with and try to see boundary conditions that could trip your code up. For instance with the alphabet_sort example, a list is expected as input. What will the code do if the list is empty? What will the function do if something that is not a string is in the list? What will it do if it's given 'None' instead of a list, or an integer? The code doesn't have to survive all these cases, but it should have an answer for each, even if the answer is just 'raise an exception'.

Deciding where to draw the line for what your code should recover from is an important design decision and your test cases should act as documentation for what you've decided to do under these sorts of unusual circumstances. For alphabet_sort, for example, it may be decided that the code will only handle lists and ONLY lists of strings. In which case, you could ignore these other edge cases entirely but it would be even better to write a test showing that your code raises a (hopefully descriptive) exception when they're encountered instead of doing something silly. So for example, the following tests could be added to the test cases for 'alphabet_sort':

```python
# An edge case that the function must handle correctly
def test_alphabet_sort_handles_empty_list():
    expected = []
    result = mypackage.alphabet_sort([])
    assert result == expected

# Cases that the function can just crash on. These tests document the fact that
# these inputs are explicitly not accepted and NOT that the programmer just
# forgot about / didnt think about them

# raises can be imported from nose.tools and will cause a test to pass when it
# raises an exception of the type stated. Use 'Exception' to succeed when
# any exception at all is raised
@raises(Exception)
def test_alphabet_sort_raises_exception_when_list_of_mixed_types_given():
    input = ["bee", 123, "apple"]
    result = mypackage.alphabet_sort(input)

@raises(Exception)
def test_alphabet_sort_raises_exception_when_not_given_list():
    input = None
    result = mypackage.alphabet_sort(input)
```

### Writing Test Classes versus Test Functions

When you're writing your tests you have two main ways you can write each test case: as a standalone function, or as a method on a class that inherits from `unittest.TestCase`.

```python
# A test case as a standalone function
def test_my_first_function():
    ...

# Test cases as methods on a class
class TestMyFunction(unittest.TestCase):

    def test_my_first_function(self):
        ...

    def test_my_second_function(self):
        ...
```

What's the difference? If you write test functions you may have a difficult time getting automated test discovery packages to run your setUp/tearDown before/after each test. A test class on the other hand will cause these packages to check for them as each test runs, just add them as methods on the classes where they're needed. In addition, using a class means you can easily organize your tests in ways that make it easier on the reader. You can group test cases that all test the same function into a single class or group tests that require the same setUp/tearDown onto one class to reduce code duplication. A test class also makes it easier for you to use more of the unittest library's features, like the [special assert methods](https://docs.python.org/2/library/unittest.html#classes-and-functions) giving you more options for how you write your tests. The only major benefit to a test function over a test class is that the code can be simpler / easier to read if you just have a few isolated tests you want to write. As a result, you will usually want to write your tests as methods on a class.

### Set Up and Tear Down

Assume we wanted to test a function called 'make_subject_dirs' that takes a list of subject IDs, reads a path from an environment variable, and then makes directories at the given path for each subject ID that matches the expected convention. As a rule, tests shouldn't affect each other and shouldn't leave any permanent changes behind after they've run. They also shouldn't require the person running the tests to manually intervene or remember a series of set up steps. So in order to write good tests in this example there are several problems to be solved:

- The function will require an environment variable to be defined, but we can't rely on the user to set this up before tests are run. Nor do we want to risk using any value the user may coincidentally have defined in their environment since this could cause weird and unpredictable behavior for the tests.
- Because a base list of subject IDs is defined as a class variable and then changed between tests (for illustration purposes, not because this needs to be the case or is good design) the contents of the list may be modified between tests and cause tests to influence each other.
- The tests will potentially leave behind folders, which pollutes the file system and could cause later test runs to pass (or fail) just because the old outputs still exist. Again, we can't and shouldn't rely on the user to clean this up.

All of these problems can be solved with set up and tear down functions as shown in the example below.

```python
class TestMakeSubjectDirs(unittest.TestCase):

    def setUp(self):
        # Creates an empty temporary directory before each test
        self.path = '/tmp/subject_dirs_test/'
        os.makedirs(self.path)

        # Point the environment variable to the temp directory
        os.environ['SUBJECT_PATH'] = self.path

        # Create the base list of subject IDs to work with
        self.input = ['S01_CMH_0001_01', 'S01_CMH_9999_02']

    def tearDown(self):
        # Deletes the temporary directory after each test
        shutil.rmtree(self.path)

    def test_make_subject_dirs_ignores_badly_named_subject(self):
        # Add a malformed subject ID to the list of subjects.
        bad_id = 'S01_CMH_01'
        self.input.append(bad_id)

        mypackages.make_subject_dirs(self.input)
        # List the contents of the output directory to see what the function did
        result = os.listdir(self.path)

        assert bad_id not in result

    def test_make_subject_dirs_creates_directories_for_specified_subjects(self):
        mypackages.make_subject_dirs(self.input)
        result = os.listdir(self.path)

        for subject_id in self.input:
            assert subject_id in result

```

A few things to note about this example:

- setUp() and tearDown() are the naming convention used in python. If you name your set up and tear down functions anything else (even just changing the capitalization or using snake case) automated packages may not find and run them.
- setUp() will run right before _every_ test. Without set up if the `test_make_subject_dirs_ignores_badly_named_subject()` test case ran before the `test_make_subject_dirs_creates_directories_for_specified_subjects()` the second test would fail because the malformed ID would still be in the input list but not in the list of results. The setUp function as written will reset the input list each time, however, making it safer to define a single base list of inputs to reuse everywhere.
- tearDown() runs once after _every_ test. However, **BEWARE** that [tearDown() is only called if setUp() succeeds](https://docs.python.org/2/library/unittest.html#unittest.TestCase.tearDown). If there's a possibility that something in your setUp() might raise an exception use a context manager and a with statement inside your test case to make sure things are cleaned up or use a try except statement to prevent it from crashing the whole set up function.

### Test Fixtures

If you don't want to, or can't, use a mock you can sometimes create a test fixture. This is just a file (or folder of files) that gets stored alongside the tests. The directory should be named in a way that indicates which test script the fixtures belong to so they can be more easily updated or deleted when no longer needed. When a new user installs the code the fixtures get neatly bundled up with them so your tests still work on other systems. Every reference to a fixture in your test code should therefore use a relative path to ensure file system layout changes don't break your tests.

A couple of things to note, though:

- **Fixtures should be read only.** All users that run your tests should get the same results, because your tests should report on the code correctness and not other factors. If your fixtures are not read only two different users running your tests on the same system could get different test results just because they don't have the same write permissions.
- **Fixtures should be kept relatively small and free of any private information.** Because the fixtures need to be shared alongside the code your fixtures may end up on github (which has file size limits) and may be seen by many people.

## Mocks

Mocks can make otherwise untestable code testable but they also make tests more complex, harder to read, and more fragile. For these reasons, in addition to showing how to use mocks, this section will aim to demonstrate how even if you _have_ to use a mock sometimes very simple changes to your code can make your life much easier and your tests much better. To do this, we'll go over writing a test for the same function written two slightly different ways.

The example we're going to use will be simple: We're going to test a function called `write_subject_ids` that takes a list of subject IDs and a full path to a file as input, filters out malformed IDs, and writes the results to the given location.

```python
def write_subject_ids(subject_ids, destination_file):
    # Filter out bad subject IDs and construct lines to write (omitted)
    ...
    lines = ...

    # Write results
    with open(destination_file, 'w') as dest_handle:
        dest_handle.writelines(lines)
```

Because the function is writing to a file there is no output returned to test against, so we have to get and read the contents to compare against what we expected it to be. We _could_ do this by letting the function write to the file system (and using a tearDown() function to delete the file after the test) but this comes with some disadvantages: where do we write to? Different operating systems have different file system layouts, so we may have to make our tests operating system specific. Do we have permissions to write there? What if the location is full and the write fails? In each of these instances our tests could fail for reasons that have nothing to do with what we're testing, which is a sign of a bad test. Using a mock addresses each of these issues.

Mocks can pose as any python object and capture any interaction so that we can make assertions about what was done. A mock can also be 'patched' in to replace module imports and functions that are used inside the function you're testing, without ever having to modify the function you're testing. This can be a bit hard to fully grasp without seeing a few examples so consider the example test below. Here we use a mock to capture `write_subject_ids` attempt to open and write our list of subject IDs (more explanation provided below the code).

```python
from mock import patch, mock_open

def test_write_subject_ids_writes_expected_contents():
    # Inputs for the function - The path can be anything
    # because the mock prevents any real files from being opened
    subjects = ['STUDY01_CMH_0001_01', 'STUD01_CMH_9999_01']
    dest_path = "/some/path/somewhere"

    # Create the mock and patch the 'open' function
    dest_file_mock = mock_open()
    with patch("__builtin__.open", dest_file_mock):
        # Note: the function call is the same as it
        # would be without a mock!
        mypackage.write_subject_ids(subjects, dest_path)

    expected_contents = "\n".join(subjects)
    dest_file_mock().writelines.assert_called_once_with(expected_contents)
```

Let's break this down a bit. `dest_file_mock = mock_open()` creates a special kind of mock used for mocking python's `open()` function. Mock_open is about the same as a normal `Mock()` or `MagicMock()` object except that it accounts for the fact that when you mock a call to 'open' you also need to mock the file handle it returns. As a result, if you call `mock_open()()` (note the double parentheses) it spits out a mock file handle with methods like 'writelines' and 'readlines' like a real python file handle.

The line `with patch("__builtin__.open", dest_file_mock):` is where the mock is actually applied. 'patch' makes sure that any reference to the `__builtin__.open` (i.e. any attempt to call python's built in 'open()' function) by any code inside the 'with' block will instead get 'dest_file_mock' given to it. This means that if we call `write_subject_ids` inside the 'with' block it would find our mock 'open' but if we called it outside it would find python's normal built in 'open' function.

To see this we could change the contents of `write_subject_ids` to print the returned file handle. We also change our test to fail (so that output is printed in the test report) and to call the function twice, once inside patch and once outside, as shown below.

```python
# Inside 'mypackage.py
def write_subject_ids(subject_ids, destination_file):
    ...
    with open(destination_file, 'w') as dest_handle:
        print(dest_handle)

# Inside test_mypackage.py
def test_write_subject_ids_writes_expected_contents():
    ...
    # Contents unchanged
    ...
    dest_file_mock = mock_open()
    with patch("__builtin__.open", dest_file_mock):
        # Note: the function call is the same as it
        # would be without a mock!
        mypackage.write_subject_ids(subjects, dest_path)

    # This one gets a real path so the open doesn't fail
    mypackage.write_subject_ids(subjects, "/tmp/test_file")
    assert False
```

If the test were run after these updates you would get output ending with something like the following from nose (the 'id' in the first line and the memory address in the second line obviously will differ):

```shell
-------------------- >> begin captured stdout << ---------------------
<MagicMock name='open()' spec='file' id='139697228016400'>
<open file '/tmp/test_file', mode 'w' at 0x7f0dcc41db70>

--------------------- >> end captured stdout << ----------------------
```

You can clearly see that the first time `write_subject_ids` runs from inside the `with patch(...)` block `print(dest_handle)` receives a 'MagicMock' object, and the second time outside of the block it receives a normal file handle. It's a good idea to use print statements like this to verify that your patches are correctly being applied and your mocks are doing what you think.

Finally lets break down the last line from the original example:

```python
dest_file_mock().writelines.assert_called_once_with(expected_contents)
```

The `dest_file_mock()` part gives you a reference to the same mock that got stored in 'dest_handle' inside our `write_subject_ids` function when it tried to use 'open()'. Using `dest_file_mock().writelines` allows you to retrieve information about how (if at all) the writelines method was used on 'dest_handle' inside `write_subject_ids`. We're then using MagicMock's built in 'assert_called_once_with()' to make an assertion about what argument 'writelines' was called with. Like all the other MagicMock/Mock methods that contain the word 'assert' that built in method is a wrapper for an assert statement and therefore our last line doesn't need to explicitly use the assert keyword.

You can see exactly what was called on most types of mocks using the 'call_args' attribute, and can see exactly which arguments were used for a method with the 'method_args' attribute. If we modify the end of our original test to print these attributes we can see exactly what `write_subject_ids` did with our mock (and would have done with a real file). Again, we end the test with 'assert False' so we actually get a report of the output.

```python
def test_write_subject_ids_writes_expected_contents():
    # Inputs for the function - The path can be anything
    # because the mock prevents any real files from being opened
    subjects = ['STUDY01_CMH_0001_01', 'STUD01_CMH_9999_01']
    dest_path = "/some/path/somewhere"

    # Create the mock and patch the 'open' function
    dest_file_mock = mock_open()
    with patch("__builtin__.open", dest_file_mock):
        # Note: the function call is the same as it
        # would be without a mock!
        mypackage.write_subject_ids(subjects, dest_path)

    expected_contents = "\n".join(subjects)

    print("Open's call args: {}".format(dest_file_mock.call_args))
    print("File handle's call args: {}".format(dest_file_mock().method_calls))
    assert False
```

When we run this with nose we get the following output:

```shell
-------------------- >> begin captured stdout << ---------------------
Open's call args: call('/some/path/somewhere', 'w')
File handle's call args: [call.writelines('STUDY01_CMH_0001_01\nSTUD01_CMH_9999_01')]
--------------------- >> end captured stdout << ----------------------
```

Hopefully, it is clear what this mock was doing in this case. This example could be simplified a bit, as I'll show in the next section.

### Mocks and Dependency Injection

The example from the previous section could have been made a bit easier if we had made use of a design principle known as 'dependency injection'. Despite the fancy name, dependency injection really just means that instead of creating objects inside your functions you pass in a reference to an object that was created elsewhere. This makes testing far easier, because you can use mocks without using patches (it's _surprisingly_ easy to patch the wrong thing sometimes), and makes your code more reusable because you don't have objects hard coded into them (i.e. you can make use of object oriented programming techniques like sub-classing!).

So for our example from the last section this would just mean writing `write_subject_ids` as shown below.

```python
def write_subject_ids(subject_ids, dest_handle):
    # Filter out bad subject IDs and construct lines to write (omitted)
    ...
    lines = ...

    # Write results
    dest_handle.writelines(lines)
```

Instead of opening the file itself it now gets the file handle as an argument from whatever function calls it. It does exactly the same thing as before but as a result, we can now rewrite our earlier test as:

```python
from mock import MagicMock

def test_write_subject_ids_writes_expected_contents():
    # Inputs for the function - The path can be anything
    # because the mock prevents any real files from being opened
    subjects = ['STUDY01_CMH_0001_01', 'STUD01_CMH_9999_01']
    dest_path = "/some/path/somewhere"

    # Create the mock as a fake file and give it as the file handle
    dest_file_mock = MagicMock(spec=file)
    mypackage.write_subject_ids(subjects, dest_file_mock)

    expected_contents = "\n".join(subjects)
    dest_file_mock.writelines.assert_called_once_with(expected_contents)
```

No more patch needed, and we dropped the weird parentheses from `dest_file_mock().writelines...`, which are surprisingly easy to forget and could have completely changed the meaning of our original test had we forgotten them.

In this case this change may not seem like a huge improvement but imagine making some small changes to our original function. Imagine it takes a path from a 'config' object that reads a settings file (like datman's dm_config for example) to check whether subjects have a folder at that path and that it reads some value from the user's shell environment to determine which naming convention to check for. In this scenario, we now have two more things to patch out in order to write a working test: the config object and os.environ. In order to write the same simple test case that we had before, we now have three nested patches, as shown below.

```python
def test_write_subject_ids_writes_expected_contents():
    ...
    dest_file_mock = mock_open()
    # Create the two new mocks that are needed
    env_mock = MagicMock()
    config_mock = MagicMock()

    # env_mock and config_mock set up is omitted here
    # see the 'mocks that return something' section

    with patch("__builtin__.open", dest_file_mock):
        with patch("os.environ", env_mock):
            with patch("datman.config", config_mock):
                mypackage.write_subject_ids(subjects, dest_path)

    ...
```

We could improve things slightly by using the patch decorator (discussed in the [Patches section](#patches)) but this still leads to some pretty deeply nested, confusing, and fragile code. Try the same test with dependency injection instead, where the config object and environment variable get passed into the function we're testing:

```python
def test_write_subject_ids_writes_expected_contents():
    ...
    dest_file_mock = MagicMock(spec=file)
    # Create the two new mocks that are needed
    env_mock = MagicMock()
    config_mock = MagicMock()

    # Mock set up is omitted here, see the 'mocks that return
    # something' section for more info

    mypackage.write_subject_ids(subjects, dest_file_mock, env_mock, config_mock)

    ...
```

Now our test can provide all three dependencies about as easily as any other arguments we pass in and it's much clearer what we're doing with our mocks.

### Patches

A patch will temporarily hide all references to some real code (whether a whole package, a class, or a function) and instead supply a mock when something tries to use that code. There are two ways to use a patch when writing tests: As a decorator or as part of a 'with' statement. If you use the decorator you can apply it to a function, a method, or an entire class. The decorator can make your code more readable but there are a couple of things to be aware of while using it. These pitfalls will be discussed further below.

When using a patch you should be _certain_ that you're patching what you think you are. This is not always as straight forward as you would expect. To help demonstrate this, imagine you have the following code to test, and a test script with the import statements shown:

```python
# Inside a script called 'utils.py'
import os.path as paths

def get_path(...):
    ...
    if paths.exists(...):
        ...
    ...

# Inside the test script where you want to add a test for 'utils.get_path'
import utils as my_utils

@patch('??????')
def test_get_path(mock_exists):
    ...
```

You want to test the code inside `utils.get_path`'s 'if' statement, but you don't want to make an actual file/directory every time the test runs just to get that code to execute so you must use a patch. But what do you patch here?

- `@patch('paths.exists')` **does NOT work** because that nickname for 'os.path' doesn't mean anything to code written outside 'utils.py' causing your test to raise an error: `TypeError: Need a valid target to patch. You supplied: 'paths'`
- `@patch('my_utils.paths.exists')` **does NOT work** because even though your test script imported 'utils.py' as 'my_utils' that name doesn't mean anything to patch causing it raise an error: `ImportError: No module named my_utils`
- Using `@patch('os.path')` to patch the entire 'os.path' library **will APPEAR to work** but utils.py is accessing the contents of the library indirectly using it's 'paths' nickname, allowing it to quietly circumvent the patch and continue to access the real function.
- `@patch('utils.paths.exists')` **WILL work** because it intercepts utils.py's only way to reference 'os.path.exists', while code that references this function under another nickname (or through 'os.path.exists' directly) will still find the real function.
- `@patch('os.path.exists')` **WILL work** because it will patch any reference to this function under any name.

To be really sure that your patch is working you can always use a print statement inside the function you're testing to print what it will run. For our earlier example, adding a `print(paths.exists)` (Note there are no parentheses after 'exists', we're not running the function we're just printing what it points to) before the if statement inside 'utils.get_path' and then running our test would give us one of these two outputs depending on whether our patch worked:

```
# If the patch is NOT correct it returns a function
<function exists at 0x7f2b8d9c72a8>

# If the patch is correct it returns a MagicMock
<MagicMock name='exists' id='140403773388880'>
```

#### Patch Decorator Pitfalls

**1. Decorated functions need an argument to hold the mock that is produced.** If you forget this you'll get an error about the number of arguments not matching what was expected.

```python
@patch(...)
def test_myfunction(mock_arg_here, ...):
    ...
```

If you use the decorator on an entire class, every method will require an argument like this even if it doesn't actually need the patch.

```python
@patch(...)
class TestMyFunction(unittest.TestCase):
    def test_one(mock_arg, ...):
        ...
    def test_two(mock_arg, ...):
        ...
```

**2. The mocks from multiple patch decorators are fed in from bottom to top.** If you mistakenly mix up the order when naming your arguments your tests can end up very, very wrong. If you're not certain, it's a good idea to stop and print your test cases' arguments because each mock, when printed, will show the name of what it is  a substitute for.

```python
@patch('function_three')
@patch('function_two')
@patch('function_one')
def test_example(mock_one, mock_two, mock_three):
    ...
```

**3. Mocks from decorators on a method are fed in before mocks from decorators on the whole class.** The bottom up order is still respected but all of the method decorators get put in first.

```python
@patch('function_a')
@patch('function_b')
class TestExampleClass(unittest.TestCase):

    @patch('function_three')
    @patch('function_two')
    @patch('function_one')
    def test_example_method(mock_one, mock_two, mock_three, mock_b, mock_a):
                   # note a and b are out of order          ^
                   # because 'function_b' is found first
        ...
```

### Mocks that Return Something

Often your mocks need to return something in order for your tests to work. For instance, if a function you're testing calls 'os.path.exists' it's not enough to just stop your code from checking the real file system the mock also needs to return either 'True' or 'False' for the rest of your code to run as it would under real conditions. This is where two MagicMock attributes, 'return_value' and 'side_effect', come in.

'return_value' will always spit out the value that you give it no matter what the function is called with.

```python
@patch('os.path.join')
@patch('os.path.exists')
def test_example(mock_exists, mock_join):
    # This will make it look like every file/folder that gets checked exists
    mock_exists.return_value = True

    # This will cause every attempt to join a path to return
    # "/tmp/mocked/path" regardless of the arguments given
    mock_join.return_value = "/tmp/mocked/path"
```

'side_effect' allows you to return different things under different circumstances. You give it a function (a lambda usually works) and the function will get called with the arguments that the mock is given when it is used.

```python
@patch('mypackage.get_path')
@patch('os.path.exists')
def test_example(mock_exists, mock_get_path):
    # Only paths ending in 'my_folder' exist
    mock_exists.side_effect = lambda x: x.endswith('my_folder')

    # Return a specific path if an expected key is found.
    # Otherwise raise a KeyError
    mock_get_path.side_effect = lambda x: {'nii': '/tmp/nii',
                                         'home': '/some/path/somewhere'}[x]
```

These attributes are relatively straight forward unless your mock is for a class instead of a function. To set side_effect or return_value for a class you have to do the following:

```python
@patch('mypackage.MyClass')
def test_example(mock_myclass):
    mock_myclass.return_value.mymethod.return_value = 'some value'
```

The first part, `mock_myclass.return_value`, is equivalent to creating an instance of a class (except in this case it is a MagicMock returning another MagicMock). To use a familiar example, if it were mocking datman's config class this would return a MagicMock that is supposed to stand in for the created 'config' object with methods like 'get_path', 'set_study', etc. The second part, `mymethod.return_value`, lets you set the return value (or side effect) for a specific method/attribute. For a mock of datman's config this would allow you to set a return value for 'get_path' or one of its other methods.

### Mock 'spec'

By default a mock will accept any attempt to interact with it. What this means is that if you patch, for instance, 'os.path' and then use the mock to call a function with a typo or a made up function your tests could still pass! Instead of raising an exception, as your code would when run normally, the MagicMock will just quietly add the function or variable to its list of available methods/attributes.

```python
# Inside mypackage.py
def my_function():
    ...
    # Note the typo: 'exit' instead of 'exists'
    os.path.exit(...)
    ...

# Inside test_mypackage.py
@patch('os.path')
def test_example(mock_paths):
    # This will not crash, even though running the function
    # normally will.
    my_function()

    # Printing dir() will show 'exit' has been added to the list of
    # methods and attribute on our mock.
    print(dir(mock_paths))
```

This happens because by default each Mock is a completely blank slate. Even when you patch the mock created has no idea what it is pretending to be, it just intercepts calls and records what was done to it.

To make your tests more robust you can use the 'spec' attribute. This will cause the mock to create an interface that matches the real object and to raise an exception when someone uses something that doesn't exist on the real object. Note that if your mock was created by a patch, you can set the spec by using 'spec=True'.

```python
mock_paths = MagicMock(spec='os.path')
# This typo will raise an AttributeError now, just like it would if you were
# using the real thing
mock_paths.exit(...)

# With a patch, you set it with spec=True
@patch('os.path', spec=True)
def test_example(mock_paths):
    # This test will now fail with an exception.
    my_function()
```

## Good Practices

- Use very descriptive / long names for your test functions. Ideally when a test fails you should be able to get a very good idea of what went wrong just from reading the name of the failing test.
- Name your tests and scripts according to the naming convention commonly used by automated test discovery packages (see [Nose](#nose) section).
- Usually, you should only use one 'assert' statement per test so that when a test fails there is only one possible problem being captured. (There are some good exceptions to this that you'll work out as you get experience, but it's a good rule of thumb)
- Tests should be completely isolated from each other. If two tests modify some sort of shared environment then use set up and tear down functions to reset the environment after each test.
- Control the environment your tests run in completely. If a test reads an environment variable your set up functions should explicitly set it to something expected so that a user who coincidentally has the same environment variable doesn't experience weird and unexpected behavior. If your tested function reads a file, you should either mock the contents of the file or provide a file to test against in a test fixture. If your tests have to communicate with other programs, with databases, or across the internet use mocks to ensure your tests are running under controlled conditions.
- Unit tests are meant to run very quickly, so the user can run them repeatedly while they're modifying code. If your tests for a single script take more than a few minutes to run perhaps consider moving the slowest of them into your integration tests which can be run much less frequently.
- Unit tests shouldn't leave any permanent changes behind. It should be as if they were never run.
- Tests shouldn't require manual set up or input from the user. `nosetests $path_to_test_folder` or `pytest $path_to_test_folder` should be all the user needs to do for your tests to run to completion and do so every time.

**REMEMBER:** Your tests should *NOT* fail because the environment or order of the tests changes. Test failures should always be because the code changed and incorrect behavior has been introduced.
