---
layout: default
title:  "Algorithms To Live By"
permalink: /content/algorithms-to-live-by.html
---

# Algorithms To Live By
{:.no_toc}

* TOC
{:toc}

## Optimal Stopping
 - Do not stop too early
 - Do not stop too late

### Look and Leap
- Look
    - Set a predetermined amount of candidates or time
    - Only gather data
- Leap
    - Pick the first candidate that outshines all the candidates in look phase

### The Secretary Problem
[Problem Definition](https://en.wikipedia.org/wiki/Secretary_problem)

#### Optimal Stopping Simulation Using Core Python - 3 Secretaries - 1,000,000 runs
- Do not hire the 1<sup>st</sup> candidate
- Hire the 2<sup>nd</sup> candidate if they are better compared to 1<sup>st</sup>
- Otherwise hire the 3<sup>rd</sup> candidate

```python
import random

random.seed(42)

iteration_count = 1000000
right_choice_made = 0

for i in range(iteration_count):
    assigned_nums = []

    while len(assigned_nums) != 3:
        rand_ordinal = random.randint(0, 2)
        if rand_ordinal in assigned_nums:
            continue
        assigned_nums.append(rand_ordinal)

    if assigned_nums[2] > assigned_nums[1]:
        right_choice_made += 1

print(right_choice_made / iteration_count) # 0.500469
```

#### Optimal Stopping Simulation Using Pandas - 100 Secretaries - 1,000,000 runs
Do not hire any candidate from the first 37 percentile, store the value of best in this phase. Starting from 38 percentile, hire the first candidate encountered where the candidate is better compared to best observed in first 37 percentile.
Following this strategy will lead to hiring the best candidate 37% of the time, the best you can have.

```python
import numpy as np
import pandas as pd

rng = np.random.RandomState(42)

numOfSecretaries = 100
numOfTries = 1000000
optimal_stopping_location = int(numOfSecretaries * 0.37)

secretaries = pd.DataFrame(rng.choice(np.arange(0, 100000), size=[numOfSecretaries, numOfTries]))
training = secretaries[0:optimal_stopping_location]
threshold = training.max()

decision = secretaries[optimal_stopping_location:].reset_index(drop=True)

df = decision[decision > threshold]
df = df.idxmax() == df.apply(pd.Series.first_valid_index).values
print(np.sum(df) / numOfTries) # 0.370514
```

__Walkthrough__
An explanation of what is going on in the above implementation with a smaller set of data: 15 candidates, 5 runs.

```python
import numpy as np
import pandas as pd

rng = np.random.RandomState(42)

numOfSecretaries = 15
numOfTries = 5
optimal_stopping_location = int(numOfSecretaries * 0.37)

secretaries = pd.DataFrame(rng.choice(np.arange(0, 100), size=[numOfSecretaries, numOfTries]))
# Initial DataFrame representing secretary points.
print(secretaries)
#      0   1   2   3   4
# 0   51  92  14  71  60
# 1   20  82  86  74  74
# 2   87  99  23   2  21
# 3   52   1  87  29  37
# 4    1  63  59  20  32
# 5   75  57  21  88  48
# 6   90  58  41  91  59
# 7   79  14  61  61  46
# 8   61  50  54  63   2
# 9   50   6  20  72  38
# 10  17   3  88  59  13
# 11   8  89  52   1  83
# 12  91  59  70  43   7
# 13  46  34  77  80  35
# 14  49   3   1   5  53

training = secretaries[0:optimal_stopping_location]
# DataFrame we will be using to adjust our threshold value.
print(training)
#     0   1   2   3   4
# 0  51  92  14  71  60
# 1  20  82  86  74  74
# 2  87  99  23   2  21
# 3  52   1  87  29  37
# 4   1  63  59  20  32

threshold = training.max()
# Our threshold value for each run.
print(threshold)
# 0    87
# 1    99
# 2    87
# 3    74
# 4    74
# dtype: int64

decision = secretaries[optimal_stopping_location:].reset_index(drop=True)
# DataFrame where we will be picking from. Remember: Pick the first value greater than threshold.
print(decision)
#     0   1   2   3   4
# 0  75  57  21  88  48
# 1  90  58  41  91  59
# 2  79  14  61  61  46
# 3  61  50  54  63   2
# 4  50   6  20  72  38
# 5  17   3  88  59  13
# 6   8  89  52   1  83
# 7  91  59  70  43   7
# 8  46  34  77  80  35
# 9  49   3   1   5  53

# Anything below here is simply me trying to figure out the first value greater than threshold.
# masked is a DataFrame where values lower than threshold are NaN
masked = decision[decision > threshold] # type: pd.DataFrame
print(masked)
#       0   1     2     3     4
# 0   NaN NaN   NaN  88.0   NaN
# 1  90.0 NaN   NaN  91.0   NaN
# 2   NaN NaN   NaN   NaN   NaN
# 3   NaN NaN   NaN   NaN   NaN
# 4   NaN NaN   NaN   NaN   NaN
# 5   NaN NaN  88.0   NaN   NaN
# 6   NaN NaN   NaN   NaN  83.0
# 7  91.0 NaN   NaN   NaN   NaN
# 8   NaN NaN   NaN  80.0   NaN
# 9   NaN NaN   NaN   NaN   NaN

# Now that we have `masked`, we will actually be picking the first !NaN value.
# For example for 0th run we will be picking 90, since that is the first value greater than our threshold.
# However, in this case, we are not actually picking the best candidate we can..
# There is a better candidate at index 7 with a value of 91! Tough luck..

# Basically the first index that is actually a value..
index_of_candidates_we_picked = masked.apply(pd.Series.first_valid_index).values
print(index_of_candidates_we_picked)
# [ 1. nan  5.  0.  6.]

# index of the actual best candidate..
idxmax = masked.idxmax()
print(idxmax)
# 0    7.0
# 1    NaN
# 2    5.0
# 3    1.0
# 4    6.0
# dtype: float64

# Have we made the right choice?
have_me_made_the_right_choice = (idxmax == index_of_candidates_we_picked)
print(have_me_made_the_right_choice)
# 0    False
# 1    False
# 2     True
# 3    False
# 4     True

# Finding the number of times we made the best choice possible at this point is very easy..
number_of_right_choices = np.sum(have_me_made_the_right_choice)
print(number_of_right_choices)
# 2

# And so is finding the percentage..
print(number_of_right_choices / numOfTries)
# 0.4
```

## Sorting
### How to Match Socks from a Laundry Bag?
Repeat the following until no socks left in the bag:
- Pick 1<sup>st</sup> random sock from the bag
- Pick 2<sup>nd</sup> random sock from the bag 
- If socks match remove both and go to first step
- If socks do not match toss 2<sup>nd</sup> sock back in and go to second step

With just 10 different pair of socks, following this method will take on average 19 pulls merely to complete the first pair. 

#### Finding Average Pulls Required

```python
import random
from operator import add


def get_pair_of_socks(num_of_socks):
    return random.sample(range(num_of_socks), num_of_socks)


def index_to_pull_sock_from(bag_of_socks: list):
    return random.randint(a=0, b=len(bag_of_socks) - 1)


def attempt_counts_matching_socks(num_of_socks_to_consider):
    # Keep attempt counts in this list.
    attempt_counts = []

    # Generate pairs of random socks.
    socks = get_pair_of_socks(num_of_socks_to_consider) + get_pair_of_socks(num_of_socks_to_consider)

    while len(socks) != 0:
        # Pick one pair from the bag..
        first_pair = socks.pop(index_to_pull_sock_from(socks))

        # Pick a second pair..
        random_pick = index_to_pull_sock_from(socks)
        second_pair = socks[random_pick]

        # We did an attempt..
        attempt_count = 1

        # If they matched, perfect. We will never enter this block.
        # Otherwise loop until you do find the match..
        while second_pair != first_pair:
            # Increment the attempt_count whenever you loop..
            attempt_count = attempt_count + 1
            random_pick = index_to_pull_sock_from(socks)
            second_pair = socks[random_pick]

        # Remove the second matching pair from the bag..
        socks.pop(random_pick)

        # Keep the number of attempts it took you to find the second pair..
        attempt_counts.append(attempt_count)

    return attempt_counts


num_of_iterations = 1000
pair_of_socks = 10

# Initalise a list full of zeros of length `pair_of_socks`
attempt_counts_so_far = [0] * pair_of_socks

for _ in range(num_of_iterations):
    # Get attempt counts for 1 iteration..
    attempt_counts_single_iteration = attempt_counts_matching_socks(pair_of_socks)

    # Add the attempt counts aligned by index. 
    # We will be dividing by the total number of iterations later for averages.
    attempt_counts_so_far = list(map(add, attempt_counts_so_far, attempt_counts_single_iteration))

average_takes = list(map(lambda x: x / num_of_iterations, attempt_counts_so_far))
print(average_takes)
# [18.205, 16.967, 14.659, 12.82, 11.686, 9.444, 7.238, 4.854, 2.984, 1.0]
```

#### Matching vs Sorting
But is matching socks from a laundry bag really identical to (or a good real life analogy of) sorting? Obviously you can not sort your socks but imagine there were numbers between 0 to 19 in the bag. 

How would matching socks be identical to sorting?
- Must you start from number 0 and find 1? Then find 2 and find 3 and so on?
- Must you find any even number and find the next number?

### Big-O Notation
- Inexact by design
- Rather than expressing an algorithm's performance in minutes and seconds, Big-O notation provides a way to talk about the kind of relationship that holds between the size of the problem and the program's running time

#### Big-O Notation for Sorting
- Even just confirming that a list to be sorted is sorted would be `O(n)`
- Comparing each item with other is `O(n²)`
- The best we can achieve is something between `O(n)` and `O(n²)`

### Bubble Sort
- Time complexity `O(n²)` 
  - As the size of the list that is being sorted increases by a multiple of 2, time complexity increases by n² = 4

#### Bubble Sort Implementation in Python
Note how comparison count increases roughly by 4 `(6, 30, 132)` as the length of the lists increase by 2 `(3, 6, 12)`.

```python
def bubble_sort(list_to_sort):
    comparison_count = 0
    unsorted = True
    while unsorted:
        unsorted = False
        for i in range(len(list_to_sort) - 1):
            comparison_count = comparison_count + 1
            if list_to_sort[i] > list_to_sort[i + 1]:
                unsorted = True
                list_to_sort[i + 1], list_to_sort[i] = list_to_sort[i], list_to_sort[i + 1]

    return list_to_sort, comparison_count

print(bubble_sort([3, 2, 1]))
# ([1, 2, 3], 6)

print(bubble_sort([6, 5, 4, 3, 2, 1]))
# ([1, 2, 3, 4, 5, 6], 30)

print(bubble_sort([12, 11, 10, 9, 8, 7, 6, 5, 4, 3, 2, 1]))
# ([1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12], 132)
```

### Insertion Sort
#### Insertion Sort Implementation in Python

```python
def insertion_sort(list_to_sort):
    i = 1
    while i < len(list_to_sort):
        temp = list_to_sort[i]
        j = i
        while j !=  0 and list_to_sort[j - 1] > temp:
            list_to_sort[j] = list_to_sort[j - 1]
            j = j - 1
        list_to_sort[j] = temp
        i = i + 1
    return list_to_sort

print(insertion_sort([5, 4, 3, 2, 1]))
# [1, 2, 3, 4, 5]
```

### Merge Sort
- Time complexity `O(n log n)`

> Merge Sort is as important in the history of sorting as sorting in the history of computing.

#### Merge Sort Implementation in Python

```python
def sort_merge_step(list_01: list, list_02: list = None):
    if list_02 is None:
        return list_01

    merge_sorted = []
    i = 0
    j = 0

    while i < len(list_01) and j < len(list_02):
        if list_01[i] <= list_02[j]:
            merge_sorted.append(list_01[i])
            i = i + 1
        else:
            merge_sorted.append(list_02[j])
            j = j + 1

    while i < len(list_01):
        merge_sorted.append(list_01[i])
        i = i + 1

    while j < len(list_02):
        merge_sorted.append(list_02[j])
        j = j + 1

    return merge_sorted


def sort_merge(a_list: list):
    if len(a_list) < 2:
        return a_list

    mid = int(len(a_list) / 2)

    left_half = sort_merge(a_list[0: mid])
    right_half = sort_merge(a_list[mid:])

    return sort_merge_step(left_half, right_half)

print(sort_merge([5, 1, 3, 8, 11, 2]))
# [1, 2, 3, 5, 8, 11]
``` 

### Quotes / Statements
> Whether it's finding the largest or the smallest, the most common or the rarest, tallying, indexing, flagging duplicates, or just plain looking for the thing you want, they all generally begin under the hood with a sort.

I do not agree with this statement, since either finding the largest or the smallest, the most common or the rarest can easily be done without sorting. For finding the largest or the smallest, sorting may be useful, but it is definetly not useful at all for the most common or the rarest.

> With sorting, size is a recipe for disaster: perversely, as a sort grows larger, the unit cost of sorting, instead of falling, rises.

Well, apparently!

> Sorting five shelves of books will take not five times as long as sorting a single shelf, but twenty-five times as long.

I think what is meant is _"Sorting __a shelf five times longer__ will take twenty-five times longer."_

### References
- [Know Thy Complexities!](http://bigocheatsheet.com/)
- ["Sorting Out Sorting" -- Baecker, Ronald M., with the assistance of David Sherman](https://www.youtube.com/watch?v=SJwEwA5gOkM)

## Caching

The idea of keeping around pieces of information that you refer to frequently.

The goal of cache management is to minimize the number of times you can not find what you are looking for in the cache. Not being able to find what you are looking for in the cache is named as a _page fault_ or a _cache miss_.

> A big book is a big nuisance.
>
> _Callimachus_

I am not sure how this quote is related to caching really. It reminds me the following quotes, which I also like:

> A designer knows he has achieved perfection not when there is nothing left to add, but when there is nothing left to take away.
>
> Antoine de Saint-Exupery

It also reminds me a quote from [The Information: A History, a Theory, a Flood](https://www.goodreads.com/book/show/8701960-the-information), which I can not exactly remember but goes something like..

> Too much information is just as bad as no information.

### Cache Eviction
Cache eviction is the process of deciding what to remove from the cache when it is capacity is full but a new item needs to be cached.

The optimal cache eviction policy is to evict the item we will need again _the longest from now_.

#### Cache Eviction Policies
- Random Eviction
- First in First Out
- Least Recently Used

## Scheduling

> How we spend our days is, of course, how we spend our lives.
>
> Annie Dillard

<hr />

> We are what we repeatedly do.
>
>Aristotle

### Inconsistency in Time Management Best Sellers
<table>
    <tr>
        <th style="width:40%">Source</th>
        <th style="width:60%">Suggestion</th>
    </tr>
    <tr>
        <td>Getting Things Done</td><td>Immediately do a task that would take 2 minutes or less</td>
    </tr>
    <tr>
        <td>Eat That Frog!</td><td>Begin with the most difficult task and move to easier ones</td>
    </tr>
    <tr>
        <td>The Now Habit</td><td>First schedule your social engagements, fill the gaps with work</td>
    </tr>
    <tr>
        <td>William James</td><td>There is nothing so fatiguing as the eternal hanging on of an uncompleted task</td>
    </tr>
    <tr>
        <td>Wait by Frank Partnoy</td><td>Deliberately do not do things right away, wait on them</td>
    </tr>
</table>


### Multiple Machine Scheduling
- [Johnson's rule](https://en.wikipedia.org/wiki/Johnson%27s_rule) is a method of scheduling jobs in two work centers
- Goal is to finish running all the tasks in the shortest time possible

#### Johnson's rule Algorithm
- List the jobs and their durations at each work center
- Select the job with the shortest duration
  - If that activity duration is for the first work center, then schedule the job first
  - If that activity duration is for the second work center then schedule the job last
- Eliminate the shortest job from further consideration
- Repeat steps 2 and 3, working towards the center of the job schedule until all jobs have been scheduled

#### Example Implementation (Java)

```java
class Task {
    String name;
    double workHoursA; // first workcenter
    double workHoursB; // second workcenter

    Task(String name, double workHoursA, double workHoursB) {
        this.name = name;
        this.workHoursA = workHoursA;
        this.workHoursB = workHoursB;
    }

    double minimumWork() {
        return workHoursA < workHoursB ? workHoursA : workHoursB;
    }

    @Override
    public String toString() {
        return "Task{" + name +'}';
    }
}

// Schedule the following tasks
final List<Task> tasks = Arrays.asList(
        new Task("A", 3.2, 4.2),
        new Task("B", 4.7, 1.5),
        new Task("C", 2.2, 5.0),
        new Task("D", 5.8, 4.0),
        new Task("E", 3.1, 2.8)
);

// Sort tasks by minimum work needed. It can be either workA or workB.
List<Task> collect = tasks.stream().sorted(Comparator.comparing(Task::minimumWork)).collect(Collectors.toList());

// Tasks to be scheduled first
List<Task> scheduled = new ArrayList<>();

// Use a first in last out to push the items to be scheduled last
// Retrieve them by popping each later to scheduled
ArrayDeque<Task> lastToDo = new ArrayDeque<>();

collect.forEach(task -> {
    if (task.workHoursA < task.workHoursB) {
        scheduled.add(task);
    } else {
        lastToDo.push(task);
    }
});

while (!lastToDo.isEmpty()) {
    scheduled.add(lastToDo.pop());
}

// scheduled:
// [Task{C}, Task{A}, Task{D}, Task{E}, Task{B}]
```

### Single Machine Scheduling
If we have a list of tasks and the only a single machine (unlike the example above), no matter how we order the tasks we can not optimize finishing running the all tasks in terms of shortest time.
However, if every task has a deadline, we can
- Optimize for the minimum delay
  - i.e. We do not care how many tasks are delayed, we want them to be delayed by minimum amounts
- Optimize for the minimum number of delayed tasks
  - i.e. We want as few as possible tasks to delayed, but we do not care the delay amount on the tasks that are delayed
- Optimize for getting individual tasks done as quick as possible
  - i.e. Increment the count of _tasks done_

#### Earliest Due Date - Minimizing the Total Delay Duration
- Order all tasks by deadline
- Execute each one by one

#### Moore's Algorithm - Minimizing the Total Delayed Task Count
- Start just like Earliest Due Date
- Whenever you encounter a task that you will not finish in time, skip it and do it at the very end

#### Shortest Processing Time - Maximizing the Finished Task Count
- Always do the shortest task first, ignoring the deadline
- This will lead to fastest removal of things from the to-do list

__Earliest Due Date vs Moore's Algorithm Example in Java__

```java
class Task {
    int requiredTime;
    int deadLine;

    public Task(int requiredTime, int deadLine) {
        this.requiredTime = requiredTime;
        this.deadLine = deadLine;
    }

    @Override
    public String toString() {
        return "Task{" +
                "requiredTime=" + requiredTime +
                ", deadLine=" + deadLine +
                '}';
    }
}

class TaskExecution {
    Task task;
    int startTime;
    int endTime;

    int delay() {
        if (endTime < task.deadLine) {
            return 0;
        }
        return endTime - task.deadLine;
    }

    boolean isDelayed() {
        return delay() > 0;
    }

    @Override
    public String toString() {
        return "TaskExecution{" +
                "task=" + task +
                ", startTime=" + startTime +
                ", endTime=" + endTime +
                ", isDelayed=" + isDelayed() +
                ", delay=" + delay() +
                '}';
    }
}

List<Task> tasks = new LinkedList<>(Arrays.asList(
        new Task(2, 3),
        new Task(3, 4),
        new Task(6, 8),
        new Task(4, 10)));

tasks.sort(Comparator.comparing(task -> task.deadLine));

int currentTime = 1;
List<TaskExecution> taskExecutions = new ArrayList<>();

while (!tasks.isEmpty()) {
    Task currentTask = tasks.remove(0);
    currentTime = executeTask(currentTime, taskExecutions, currentTask);
}

List<TaskExecution> delayedExecutions; 
delayedExecutions = taskExecutions.stream().filter(TaskExecution::isDelayed).collect(toList());
System.out.println(taskExecutions);

tasks = new LinkedList<>(Arrays.asList(
        new Task(2, 3),
        new Task(3, 4),
        new Task(6, 8),
        new Task(4, 10)));

tasks.sort(Comparator.comparing(task -> task.deadLine));

currentTime = 1;
taskExecutions = new ArrayList<>();

List<Task> delayedTasks = new ArrayList<>();
while (!tasks.isEmpty()) {
    Task currentTask = tasks.remove(0);
    if (currentTime + currentTask.requiredTime > currentTask.deadLine) {
        delayedTasks.add(currentTask);
    } else {
        currentTime = executeTask(currentTime, taskExecutions, currentTask);
    }
}

while (!delayedTasks.isEmpty()) {
    Task currentTask = delayedTasks.remove(0);
    currentTime = executeTask(currentTime, taskExecutions, currentTask);
}

delayedExecutions = taskExecutions.stream().filter(TaskExecution::isDelayed).collect(toList());
System.out.println(taskExecutions);

private static int executeTask(int currentTime, List<TaskExecution> taskExecutions, Task currentTask) {
    TaskExecution taskExecution = new TaskExecution();
    taskExecution.task = currentTask;
    taskExecution.startTime = currentTime;
    int endTime = currentTime + currentTask.requiredTime;
    taskExecution.endTime = endTime;
    taskExecutions.add(taskExecution);
    currentTime = endTime;
    return currentTime;
}
```

__Report__

```
Earliest Date Algorithm:
[TaskExecution{task=Task{requiredTime=2, deadLine=3}, startTime=1, endTime=3, isDelayed=false, delay=0},
 TaskExecution{task=Task{requiredTime=3, deadLine=4}, startTime=3, endTime=6, isDelayed=true, delay=2},
 TaskExecution{task=Task{requiredTime=6, deadLine=8}, startTime=6, endTime=12, isDelayed=true, delay=4},
 TaskExecution{task=Task{requiredTime=4, deadLine=10}, startTime=12, endTime=16, isDelayed=true, delay=6}]

Moore's Algorithm:
[TaskExecution{task=Task{requiredTime=2, deadLine=3}, startTime=1, endTime=3, isDelayed=false, delay=0},
 TaskExecution{task=Task{requiredTime=4, deadLine=10}, startTime=3, endTime=7, isDelayed=false, delay=0},
 TaskExecution{task=Task{requiredTime=3, deadLine=4}, startTime=7, endTime=10, isDelayed=true, delay=6},
 TaskExecution{task=Task{requiredTime=6, deadLine=8}, startTime=10, endTime=16, isDelayed=true, delay=8}]
```

In the data above, note that:
- In Earliest Due Date Algorithm, there are 3 delayed executions whereas in Moore's Algorithm 2
- In Earliest Due Date Algorithm total amount of delay is 12 whereas in Moore's Algorithm 14
- End Time are both same: 16
- Moore's Algorithm skips executing the 2nd and 3rd tasks in favor of getting the 4rd task on time
- Moore's Algorithm causes delay amounts of `6` and `8` instead of `2` and `4` on tasks 2 and 3

None of these tasks had _weight_ (i.e. importance) associated with them in our examples. When tasks not only have deadline but also weight, things get complicated.

### Priority Inheritance
If a low-priority task is found to be blocking a high-priority resource, the low-priority task should become the highest-priority.
If you keep constantly thinking about the novel you are about to finish while studying for the exam you need to take, maybe it is better to finish the novel first, unblocking the high priority task at hand!

### Context Switch
Context Switching helps us getting things done by pausing at a state of a task, getting other things done, and getting back to it.

Computers and people face the same challenge: The machine responsible for scheduling is the machine itself that will process the tasks.
The scheduling task itself becomes a task in the to-do list which also must be scheduled.

Context Switching however is expensive: _Now where was I_. The effects of context switching can cause delays of not only seconds but minutes and errors.

### Better Scheduling
- Limit yourself to checking your messages once (or twice a day) if you are not expected to be more responsive
  - Try to stay on a single task without decreasing your responsiveness below the lowest acceptable limit, minimise context switching
- Learn to say no, i.e. refuse
  - Do not accept any more tasks if you are full, which will in theory lead to only context switching and not getting done anything at all
- If there are any low priority tasks blocking high priority tasks getting done, let the low priority task inherit priority from the high priority task

## Bayes 's Rule
### Rule of Succession
> If we repeat an experiment that we know can result in a success or failure, n times independently, and get s successes, then what is the probability that the next repetition will succeed?
>
> The more data we have, the less importance should be assigned to our prior information.
>
> [Source](https://en.wikipedia.org/wiki/Rule_of_succession)

### Probabilities and Distributions
#### Normal Distribution
- Things that tend towards some _natural_ value
  - Things in natural world, such as human life expectancy

#### Power-Law Distribution
- Things where distribution does not tend toward a _natural_ value
- Where many (many) values are one side with a particular value, a few values are on one side with a highly different value
  - Income per person
  
#### Erlang Distribution
- Distributions that yield the same value independent of any prior information
- Also called: "memoryless"
  - Hitting Blackjack has always the same probability no matter how many times you tried before

<table>
        <tr>
            <th style="width:25%">Distribution</th>
            <th>Expectation</th>
        </tr>
        <tr>
            <td>Normal Distribution</td>
            <td>The longer the incidents goes on, expect it to finish sooner.</td>
        </tr>
        <tr>
            <td>Power-Law Distribution</td>
            <td>The longer the incidents goes on, expect it to go longer.</td>
        </tr>
        <tr>
            <td>Erlang Distribution</td>
            <td>The longer the incidents goes on, assume it might finish any given time.</td>
        </tr>
</table>

### Predictions
> Knowing what distribution you are up against makes all the difference when predicting the future. When .. discovered he had cancer, he found out
> half of the patients with his form of cancer dies within the eight months. But without the distribution, eight months did not tell him much.
> If it were a normal distribution, it would be normal for him to think his chances was going lower and lower as he lived every single day after the eight months.
> But if it were a power-law distribution, then he knew the more he lived, the more likely he would live even longer.
>
> It turned out it was power-law distribution after all, and he lived twenty more years.

## Randomness
> A randomized algorithm is an algorithm that employs a degree of randomness as part of its logic. 
> The algorithm typically uses uniformly random bits as an auxiliary input to guide its behavior, 
> in the hope of achieving good performance in the "average case" over all possible choices of random bits.
> 
> [Source](https://en.wikipedia.org/wiki/Randomized_algorithm)

### Sampling
- When we want to know something about a complex quantity, we can estimate its value by sampling from it
- At least gives you an answer, compared to nothing at all

#### Buffon's needle
- Sampling the value of π by simulating dropping needles as explained in [Buffon's needle](https://en.wikipedia.org/wiki/Buffon%27s_needle)

```python
import math
import random

iteration_count = 10000000
crossed = 0

needle_length = 1.0
gap_length = 2.0


for i in range(iteration_count):
    drop_point = random.uniform(-1, 1)
    # π / 2 = 157079632679
    drop_degree_rad = random.randint(0, 157079632679) / 100000000000

    tip = (math.sin(drop_degree_rad) * needle_length / 2) + drop_point
    bottom = drop_point - (math.sin(drop_degree_rad) * needle_length / 2)

    if math.fabs(tip) >= 1 or math.fabs(bottom) >= 1:
        crossed += 1

print(2 / crossed / iteration_count / 2)  # 3.140268574610112 Very close!
```

### Prime Numbers
[Sieve of Eratosthenes](https://en.wikipedia.org/wiki/Sieve_of_Eratosthenes) 

#### Sieve of Eratosthenes Implementation - Java
```java
int upto = 100;

final Set<Integer> nonPrimeProcessed = new HashSet<>();
final Set<Integer> primes = IntStream.rangeClosed(1, upto).boxed().collect(Collectors.toSet());

for (int i = 2; i < upto; i++) {
    if (i * i > upto) {
        break;
    }
    if (nonPrimeProcessed.contains(i)) {
        continue;
    }
    int k = i;
    int mul = i * k;
    while (mul < upto + 1) {
        nonPrimeProcessed.add(mul);
        primes.remove(mul);
        k++;
        mul = i * k;
    }
}
```

#### Sieve of Eratosthenes Implementation - Python

```python
def primes(up_to):
    if up_to < 2:
        return [False] * up_to

    # 0 indexed array hence the +1 so index is aligned with the integer value.
    prime_indices = [True] * (up_to + 1)

    # Special cases 0 and 1.. Not primes..
    prime_indices[0] = False
    prime_indices[1] = False

    for i in range(2, len(prime_indices)):
        if i * i >= len(prime_indices):
            break
        mul = i
        val = i * mul
        while val < len(prime_indices):
            prime_indices[mul * i] = False
            mul = mul + 1
            val = i * mul

    return prime_indices
```

## Networking
> The term connection has a wide variety of meanings. It can refer to a physical or logical path between two entities, it can refer to the flow over the path, it can inferentially refer to an action associated with the setting up of a path, or it can refer to an association between two or more entities, with or without regard to any path between them.
> 
> _Vint Cerf and Bob Kahn_, [A Protocol for Packet Network Intercommunication](https://www.cs.princeton.edu/courses/archive/fall06/cos561/papers/cerf74.pdf)

### Circuit Switching vs Packet Switching
- Phone calls use __circuit switching__: Constant bandwidth between the sender and the receiver
  - Not suitable for computers: Computers are idle for long periods, burst for a short period of time to send data, then go idle again

> In Packet Switching, there are no connections. What you call a connection is a _consensual illusion_ between two end points.
>
> _Stuart Cheshire_

### Acknowledgment
- An impossible to solve problem: [Two Generals' Problem](https://en.wikipedia.org/wiki/Two_Generals%27_Problem)
- Good enough for TCP: [TCP three-way handshake](https://en.wikipedia.org/wiki/Handshaking#TCP_three-way_handshake)

### Exponential Backoff
- Increase wait time between tries exponentially
- Prevents completely giving up, waits longer and longer between each fails
- Used in password protections as well where systems force you to wait longer after each failed attempt

### Congestion
- Big difference between Circuit Switching and Packet Switching: The way they deal with congestion
  - Circuit Switching: Either you are accepted or not. _Gets full_
  - Packet Switching: Transmissions are delayed. _Gets slow_

### Buffer
- A buffer is a queue whose function is to smooth out bursts: A line in a coffee shop is a buffer
- A buffer will only function correctly when it is routinely zeroed out
- We think we are always connected, actually we are always buffered
- The feeling that one needs to look at everything on the Internet, read all possible books, watch all shows is called _buffer-bloat_
- Lack of idleness is the primary feature for buffers: works for machines, not for us
- Vacation email auto-responders tell senders to expect latency, that their mails are _buffered_. A better way maybe _Tail Drop_: Informing no messages will be received for a while

## Game Theory
- Just because equilibrium is stable, it does not mean it is good
  - Equilibrium in [Prisoner's Dilemma](https://en.wikipedia.org/wiki/Prisoner%27s_dilemma) would be both prisoners to betray each other wheres the best outcome is for neither to betray the other
- The equilibrium condition where everyone acts for their best interest may not be actually the best interest for the individuals of the group
  - Another example can be [Doping In Sport](https://en.wikipedia.org/wiki/Prisoner%27s_dilemma#In_sport)

## Related Pages
- [Sorting Socks and Other Practical Uses of Algorithms - Michiel Stock](https://michielstock.github.io/Algorithms/)
