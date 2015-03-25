# Debian-Clang
Adding this repository to host tasks to be performed before coding period

#Task 1: The list of packages which must be fixed to reach the objective

- I have studied dependency related flags in Debian packages, and I feel 
  that we should consider all the packages which are marked with priority 
  field : "required"

- If we go in more deep, out of all required packages, 25 packages are
  "Essential" packages, and remaining required packages are dependencies
  (direct or transitive) of these "Essential" packages, which are known
  as psedo-essential.

- And then to satisfy dependency of these "required" packages, we require:
  insserv (dependency of sysvinit)
  libsemanage1 (dependency of shadow)
  libsemanage-common (dependency of libsemanage1)
  ustr (dependency of  libsemanage-common) 

- This makes the list to be considered for our minimal bootstrapable Debian.
  Which I have uploaded as required.list.

- Now to find which packages require fixes, we can use data from clang.debian.net
  project, for now I have listed some of these in failng.list
  

