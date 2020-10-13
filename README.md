# R Package Template

## Clone repository

 - clone this repository to your desktop
 - open the `package.Rproj` to load the project into RStudio
 - you may have to `install.packages(c("usethis", "devtools", "testthat", "pkgdown"))`

## Setup Package

 - turn this repository into an R package: `usethis::use_description(check_name = FALSE)`
 - rename the package by changing the name in the `DESCRIPTION` file (note, no spaces or `-` allowed)

## Add function(s)

 - create an R Script (`math.R`) file in the `R` folder and write a simple function, e.g.:
  - ```R
    #' Add together two numbers.
    #'
    #' @param x A number
    #' @param y A number
    #' @return the The sum of \code{x} and \code{y}
    #' @examples
    #' add_numbers(1, 2) # add 1 + 2
    #' @export
    add_numbers <- function(x, y) {
      return(x+y)
    }
    ```
 - load the package directly into memory: `devtools::load_all()` (`Ctrl/Cmd` + `Shift` + `L`)
 - call your function from the console to test it, e.g. with `add_numbers(2, 5)`
 - NOTE: all package functions are available if loaded this way but only the ones that have `@export` in their documentation will be made available to your users via regular installation
 - NOTE: consistent naming conventions for package files are very useful for your users to easily find what they are looking for. I personally recommend using a 2-3 letter + `_` prefix for **all** functions in your package (e.g. `yay_add_numbers`, `yay_multiply_numbers`) because it makes it very easy for your users to use the auto-complete feature to find the right function.

## Add documentation

 - generate the documentation `devtools::document()` (`Ctrl/Cmd` + `Shift` + `D`)
 - test documentation of your function, e.g. with `?add_numbers()`
 - test the top-level documentation of your package, e.g. with `?packagename`
 - check your `NAMESPACE` to see what it contains
 - notice that besides the export instruction for the function you created (and added `@export` to), it also lists a function that is imported into your package from another packge - this is important for **all** functions you want to use from other packages so it is clear where R should go find them

## Generate online documentation

 - your documentation will always be bundled with your package for access using the `?` functions from within R but it is very helpful for your users to have use cases and function documentation easily accessible also on the web
 - this is most easily achieved using `pkgdown`, set it up with `usethis::use_pkgdown()`
 - run `pkgdown::build_site()` to demo your documentation web site
 - by default, this will generate your repository README as the front page, a reference page with all your functions and their documentation, as well as a list of articles based on your vignettes (more on vignettes later)
 - however, `pkgdown` is extremely customizable and you can specify how your page should be structured and styled using the `_pgkdown.yml` file. For details, check out the [pgkdown website](https://pkgdown.r-lib.org) (generated itself by `pkgdown`...)
 - this repo is set up to generate the `pkgdown` online documentation automatically after every push to the master branch once you enable **Actions** (in the menu at the top) for your repository and select the `gh-pages` branch as **Source** in your repository **Settings** (in the menu at the top) under **GitHub Pages**. The resulting documentation page becomes available at https://cub-computational-tools.github.io/YOUR_REPO_NAME/ once built successfully for the first time.

## Add testing

 - set up the testing environment: `usethis::use_testthat()`
 - create a test file for your math funcions with: `usethis::use_test("math.R")`
 - open the resulting test sript file (`test-math.R`) in the `test/testthat` folder and add a simple test, e.g.:
 - ```R
    test_that("addition works", {
      expect_error(add_numbers())
      expect_equal(add_numbers(1, 1), 2)
    })
   ```
 - run all package tests: `devtools::test()` (`Ctrl/Cmd` + `Shift` + `T`)
 - note: you can enable auto run of tests whenever you make a change in the code by running `testthat::auto_test_package()`

## Check entire package

 - to check proper formatting, all examples, and all tests of the package: `devtools::check()` (`Ctrl/Cmd` + `Shift` + `E`)

## Build package

 - to build and formally install the package from within this project: `devtools::install()` (`Ctrl/Cmd` + `Shift` + `B`)
 - to install it from GitHub: `devtools::install_github('USER/REPO')` (at this point other people can start using your package simply by installing it from GitHub with this simple command!)

## Continuous integration

 - continuous integration is the concept that for every code change, all the tests you wrote should be run *automatically* so problems can be identified as soon as they occur
 - to enable continuous integration, enable **Actions** (in the menu at the top) for your repository (you probably have done so already in the section on online documentation) and run `usethis::use_github_action_check_standard()` in your console. Copy the resulting badge code at the top of your README.md file.
 - push to GitHub and follow the badge link to see how well your package works on different operating systems 
 - Note: continuous integration is only as good as your tests! writing good tests prevents future problems and pays off manyfold

## Using function from other packages

### Add imports

 - the `import` statements in the `NAMESPACE` file are automatically generated from the documentation, in this case from the line `#' @importFrom methods is` in the `R/package.R` file. It is recommended to keep all your imports in one location, e.g. the `R/package.R` file.
 - add additional imports simply by adding lines such as e.g. `#' @importFrom dplyr select filter bind_rows` to import the `select`, `filter` and `bind_rows` functions from the `dplyr` package. Note that it is also possible to import an entire packages with e.g. `#' @import ggplot2` although not generally recommended since it makes it hard to figure out which functions your package actually needs.
 - additionally, all packges you import from need to be listed in the `DESCRIPTION` file - this is for the purpose of R figuring out automatically what else to install if someone installs your package
 - to add an imported package, simply add/expand the imports setting in the `DESCRIPTION` file, e.g. with
 - ```R
   Imports:
      methods,
      dplyr,
      ggplot2
   ```

### Alternative to imports

 - alternatively (and preferred by some), add packages whose functions you use in the `DESCRIPTION` file (as detailed above) but then call the functions directly via the `package::function` syntax (e.g. `dplyr::select`). That way they don't need to be imported by the NAMESPACE and you don't need to document with `@importFrom`.
 - a mix of both approaches is possible (e.g. `@importFrom` some function you use a lot and call rare functions via `package::function`)

## Add a vignette

 - vignettes are basically RMarkdown notebooks that are intended to teach your users how to use your package. They are completely optional but highly recommended if you want your users to actually understand how to best interact with your creation.
 - to start a vignette, run `usethis::use_vignette("example")` (where `example` is the file name for your vignette) and it will copy a vignette template in your `vignettes` folder. You can open and knit this vignette to see what it looks like.

## Add a license

 - this is optional when you first start but if you're planning on distributing your code it is good to attach a license that clarifies what rules you would like your code to be released under. There are many different types of licenses but two are most commonly used in open source software: the MIT license and the GNU General Public License (GPL). Both are open source and free use but differ substantially in how they regulate derived work. Basically, MIT does not impose any restrictions on derived works (i.e. someone/you could modify or commercialize your code as a closed-source application) whereas GPL requires all derived work to also be open source. Neither is inherently better, they're just different and it depends what you prefer for your work. For more details read e.g. [this article](https://www.quora.com/What-are-the-key-differences-between-the-GNU-General-Public-license-and-the-MIT-License).
 - either way, you can add each license (`LICENSE` file and corresponding entry in your `DESCRIPTION`) by running, respectively:
    - `usethis::use_mit_license()`
    - `usethis::use_gpl3_license()`

## Linting

A linter is a tool that analyzes code to flag programming and stylistic errors. It is useful to check code for readability and general programming conventions. There is a [good linter available](https://github.com/jimhester/lintr#static-code-analysis-for-r) that integrates into RStudio:
 - install with `install.packages("lintr")`
 - check that it is available in RStudio: Tools -> Addins -> Brows Addins... -> check that `lintr` is listed to lint the current open file or package
 - it's useful to hit the `Keyboard shortcuts...` at the bottom of this page to define a shortcut to lint the current file, e.g. something like `option` + `shift` + `L`

## Releasing your package

 - the advantage of using GitHub is that the latest version of your package is always available for your users directly from GitHub via `devtools::install_github('USER/REPO')` (installs the master branch)
 - additionally, people can install specific branches, release tags or even the state at individual commits using `devtools::install_github('USER/REPO', ref = "BRANCH_NAME|TAG|COMMIT#")`
 - however, for wider dissemination it can be quite helpful to have your package also hosted on CRAN (the Comprehensive R Archive Network), which makes it possible for users to quickly install pre-compiled versions using the regular `install.packages("PACKAGE")` command
 - release on CRAN has a bunch of other hoops because the R community is very protective of this centralized resource, some of the rules are a bit annoying but all serve a (historical) purpose. If you want to release your package on CRAN, I recommend checking out/following [this link](http://r-pkgs.had.co.nz/release.html)
