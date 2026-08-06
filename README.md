This repo demonstrates how to create output validators that give you a percent of the subtask score in Kattis' [Legacy](https://www.kattis.com/problem-package-format/spec/legacy.html) problem package format using a modified `testdata_tools`.

There are two common usages for this:
- There is an interactive problem with subtasks, with a couple of pass-fail groups, and one "continuous" scoring function.
- You get 50% of the points if you can decide whether a hamiltonian path exists. Full points if you can find one when it exists.

## Usage: 
- Use the `gen.sh` from this repo instead of the one provided in `testdata_tools`. 
- Put the grader from this repo into the `graders/` folder.
- Add `validation: custom score` to `problem.yaml` (and interactive if it's interactive of course).
- Now, `accept_with_score(x)` in the output validator works like a multiplier of the subtask score specified in your `generator.sh`. In all reasonable usages, this means that you should use `accept_with_score(x)` with a real number $x$ in $[0,1]$.


## Footguns (or: why you should not reinvent the wheel with this one)
In general, there are other solutions, but it's easy to shoot yourself in the foot.
- Modifying the `testdata_tools` yourself: Kattis has nontrivial, undocumented conditions for applying IOI scoring to a particular problem. This generator has been tested for this.
- Kattis avoids rerunning the same test case by the tuple (content of .in, content of .ans, output_validator_flags). Thus, being able to detect which subtask you are in *must* either result in forcing Kattis to rerun your test case in each group, or making it impossible to reuse test cases.

Some examples:
- Encoding in `.in` files: forbids reuse, but should be done if IOI scoring is not possible
- Encoding in `.ans` files: still forbids reuse, strictly worse than the last
- Encoding in `output_validator_flags`: allows reuse across groups, but forces Kattis to rerun. Least bad option out of these 3

Since they may be of independent interest, the requirements for IOI scoring to apply are:
- The problem is maximization and has subtasks.
- If `data/` has the flag `ignore_sample` set:
    - Then, the grader used to grade `secret/` must be the default grader and have aggregation mode `sum`
- Else:
    - Both the `secret/` and `sample/` graders must be the default grader and have aggregation mode `sum`

Note that `grading: custom` is an inherited property. So the proper solution is to set `grading: custom` in sample and every group, no other. Kattis' treatment of graders w.r.t. IOI scoring is extremely fragile, so be careful with doing anything fancy unless you have the Kattis source code.
