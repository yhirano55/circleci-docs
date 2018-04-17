---
layout: classic-docs
title: "Shell Scripting: Best Practices"
short-title: "Shell Script Best Practices"
description: "Best practices for writing shell scripts for CircleCI configuration"
categories: [getting-started]
order: 10
---

*[Basics]({{ site.baseurl }}/2.0/basics/) > Shell Scripting: Best Practices*

## Overview

Configuring CircleCI often requires
writing shell scripts.
While shell scripting can grant finer control over your build,
it is a subtle art
that can produce equally subtle errors.
You can avoid many of these errors
by reviewing the best practices
explained below.

### Use ShellCheck

[ShellCheck](https://github.com/koalaman/shellcheck) is a shell script static analysis tool
that saves you from yourself,
regardless of your level of shell scripting experience.

ShellCheck works best with CircleCI
when you add it as a separate job in your `.circleci/config.yml` file.
This allows you
to run the `shellcheck` job in parallel with other jobs in a workflow.

```yaml
version: 2
jobs:
  shellcheck:
    docker:
      - image: nlknguyen/alpine-shellcheck:v0.4.6
    steps:
      - checkout
      - run:
          name: Check Scripts
          command: |
            find . -type f -name '*.sh' | wc -l
            find . -type f -name '*.sh' | xargs shellcheck --external-sources
  build-job:
    ...

workflows:
  version: 2
  workflow:
    jobs:
      - shellcheck
      - build-job:
          requires:
            - shellcheck
          filters:
            branches:
              only: master
```

### Set Error Flags

There are several error flags
you can set
to automatically exit scripts
when unfavorable conditions occur.
As a best practice,
add the following flags at the beginning of each script
to protect yourself from tricky errors.

```bash
#!/usr/bin/env bash

# Exit script if you try to use an uninitialized variable.
set -o nounset

# Exit script if a statement returns a non-true return value.
set -o errexit

# Use the error status of the first failure, rather than that of the last item in a pipeline.
set -o pipefail
```

For more detailed explanations and additional techniques,
see [this blog post](https://www.davidpashley.com/articles/writing-robust-shell-scripts)
on writing robust shell scripts.