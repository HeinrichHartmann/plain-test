# plain-test

plain text testing.

This script can be used for a simple system level testing with aid of git.

## Example

file: ls.test
<pre>
#!/bin/sh
ls -l
</pre>

Now run `plain-test ls.test` and `ls.test` becomes 

file: ls.test
<pre>
#!/bin/sh
ls -l

=== stdout ===
total 24
-rwxrwxrwx 1 hartmann hartmann 631 Mar  6 17:15 curl.test
-rwxrwxrwx 1 hartmann hartmann 122 Mar  6 17:15 hellopy.test
-rwxrwxrwx 1 hartmann hartmann  16 Mar  6 17:15 ls.test

=== stderr ===

</pre>

Run `plain-test ls.test` again, and the ouput does not change.

## Explanations

Usage:

    plain-test [test-files|dirs]
    
plain-test takes a number of test-files or dirs as parameter. If dirs are provided plain-test is
executed over all test-files inside the directory.

test-files have to have ending `.test` and have the following structure:

    <code>
    === stdout ===
    <out>
    === stderr ===
    <err>
    
The parts following <code> are optional.

When applied to a test-file, the following process is triggered:
1. test-file is truncated to the <code>-part
2. test-file is executed as a file (using `os.evalvp()` syscall) 
3. output to stdout and stderr is caputred and appended to the file along with a delimiter

## Example Workflow

We want to test a REST API with a number of endpoints. For each endpoint we prepare a test-file
of the form

    #!/bin/sh
    curl -s example.com/my/endpoint?bla=foo

and put those scripts in a folder $tests. We execute all tests once using

   plain-test $tests

This will execute all scripts and therefore make the desired calls to the REST API.
Ouput is appended to the files. Check the output is as expected.

Now add those scripts to version control:

   cd $tests
   git init .
   git add *.test
   git commit -m "expected results"

To run the test suite again do

    plain-test $tests
    git diff

and you will see the difference between the current ant the expected output.
