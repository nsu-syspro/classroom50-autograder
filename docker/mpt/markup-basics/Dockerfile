# Grading image for mpt markup-basics (and other markup-flavored assignments).
#
# The assignment's setup test used to apt-get install the whole toolchain on
# every submission and timed out (360s) on a large share of runs (see
# classroom50-review ta/classrooms/mpt/plans/markup-basics-custom-image.md).
# Baking the toolchain here turns the setup test into a cheap presence probe.
#
# Requirements from the classroom50 runner (`runtime.container` support):
#   - publicly pullable image (no pull secrets in student repos);
#   - runs as root (set in assignments.json, `user: "root"`): non-root breaks
#     actions/checkout with EACCES on /__w/_temp;
#   - must ship: python3 + pip (runner.py), bash (grade steps pin shell),
#     curl (fetches runner.py), gh (commit status / release / Feedback PR),
#     git (allowed_files, review diff, timestamps), sudo (tests run
#     `sudo make taskN`), make + the markup toolchain itself.
FROM ubuntu:24.04

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update \
    && apt-get install -y \
        ca-certificates \
        curl \
        git \
        gnupg \
        make \
        pandoc \
        graphviz \
        gnuplot \
        latexmk \
        texlive-xetex \
        texlive-lang-cyrillic \
        fonts-dejavu \
        sudo \
        python3 \
        python3-pip \
    && rm -rf /var/lib/apt/lists/*

# Recommends are KEPT on purpose (no --no-install-recommends): the runner's
# old setup test installed with recommends, so student documents rely on
# what recommends pull in - e.g. pandoc's LaTeX template needs lmodern.sty
# (texlive-xetex recommends), without which task1 fails with
# "File `lmodern.sty' not found". The image is built once; fidelity wins
# over size.

# gh CLI: ubuntu:24.04's universe copy is ancient; use the official apt repo.
RUN curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg \
        -o /usr/share/keyrings/githubcli-archive-keyring.gpg \
    && echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" \
        > /etc/apt/sources.list.d/github-cli.list \
    && apt-get update \
    && apt-get install -y --no-install-recommends gh \
    && rm -rf /var/lib/apt/lists/*

# Tool-presence smoke test mirroring the assignment's setup probe: the same
# checks the setup test runs inside the graded repo, plus the runner basics.
RUN pandoc --version >/dev/null \
    && latexmk --version >/dev/null \
    && xelatex --version >/dev/null \
    && dot -V \
    && gnuplot --version \
    && make --version >/dev/null \
    && git --version \
    && gh --version \
    && python3 --version \
    && curl --version >/dev/null \
    && sudo --version >/dev/null

CMD ["/bin/bash"]
