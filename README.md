This repo demonstrates how to create output validators that give you a percent of the subtask score in Kattis' [Legacy](https://www.kattis.com/problem-package-format/spec/legacy.html) problem package format using a modified `testdata_tools`.

There are two common usages for this:
- Subtasks in interactive problems
- "You get 50% of the points if you can solve the decision problem"

## Usage: 
- Use the `gen.sh` from this repo instead of the one provided in `testdata_tools`. 
- Put the grader from this repo into the `graders/` folder.
- Add `validation: custom score` to `problem.yaml` (and interactive if it's interactive of course).
- Now, `accept_with_score(x)` in the output validator works like a multiplier of the subtask score specified in your `generator.sh`. In all reasonable usages,
this means that you should use `accept_with_score(x)` with a real number $x$ in $[0,1]$.

## Footguns (or: why you should not reinvent the wheel with this one)
In general, there are other solutions, but it's easy to shoot yourself in the foot.
- Modifying the `testdata_tools` yourself: Kattis has nontrivial, undocumented conditions for applying IOI scoring to a particular problem. This generator has been tested for this.
- Storing the subtask scores in the output validator: with this, you probably can't do subtask inclusion, and suddenly, changing the subtask scores in the generator suddenly has no effect, which is a bit of a footgun.  

Since they may be of independent interest, the requirements for IOI scoring to apply are:
- The problem is maximization and has subtasks.
- If `data/` has the flag `ignore_sample` set:
    - Then, the grader used to grade `secret/` must be the default grader and have aggregation mode `sum`
- Else:
    - Both the `secret/` and `sample/` graders must be the default grader and have aggregation mode `sum`

Note that `grading: custom` is an inherited property. So the proper solution is to set `grading: custom` in sample and every group, no other.
This is exactly what this `gen.sh` does.
