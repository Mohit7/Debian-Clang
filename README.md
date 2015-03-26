# Bootable Clang-Built Debian
Adding this repository to host tasks to be performed before coding period:

####Task 1: The list of packages which must be fixed to reach the objective:

- I have studied dependency related flags in Debian packages, and I feel
  that we should consider all the packages which are marked with priority
  field : "required"

- If we go in more deep, out of all 62 required packages, 25 packages are
  "Essential" packages, and remaining required packages are dependencies
  (direct or transitive) of these "Essential" packages, which are known
  as pseudo-essential.

- And then to satisfy dependency of these "required" packages, we require:
  - insserv (dependency of sysvinit)
  - libsemanage1 (dependency of shadow)
  - libsemanage-common (dependency of libsemanage1)
  - ustr (dependency of  libsemanage-common)

- This makes the list to be considered for our minimal bootstrapable Debian.
  Which I have uploaded as [required.list](https://github.com/Mohit7/Debian-Clang/blob/master/required.list "required list").

- Now we need find which packages are failing with Clang. For now I have used data from [clang.debian.net](http://clang.debian.net "clang.debian.net"), and listed some failing packages 
  in [failing.list](https://github.com/Mohit7/Debian-Clang/blob/master/failing.list "required list").

####Task 2: A patch to fix a bug:

- I have tried to fix [Bug #777914](https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=777914 "insserv bug") for insserv with patch [undeclare_identifier.patch](https://github.com/Mohit7/Debian-Clang/blob/master/undeclare_identifier.patch "insserv patch").
