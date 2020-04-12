---
title: "🕷️ Hexapod Simulator: A mind-boggling bug"
date: 2020-04-13T03:57:13+08:00
categories: [hexapod-simulator, hexapod-robot-simulator]
tags: [hexapod-robot-simulator, hexapod-simulator, hexapod]
draft: False
---

> - Released: [v0.2.0-alpha.1](https://github.com/mithi/hexapod-robot-simulator/releases/tag/v0.2.0-alpha.1)
> - Deployed (slow): https://hexapod-robot-simulator.herokuapp.com/inverse-kinematics

I was testing the inverse kinematics and I noticed some strange behavior. I have two versions,
technically they should output the same thing (within an error range) which they usually do,
but there are a few cases where they don't and I don't know why.
I have to investigate this.

I have a pseudo code like this
```python

# the hexapod given the dimensions, with all 18 angles = 0
base_hexapod = VirtualHexapod(dimensions)

# Given the 8 ik parameters roll, pitch yaw angles, and 3 translation
# the other 2 parameters is used to determine what
# the contact points of the the legs are to the ground
# update the new points of the hexapod
# and return the 18 computed angles as `poses`
updated_hexapod, poses = inverse_kinematics_update(base_hexapod, ik_parameters)

# Given the computed angles recreate the pose (without knowing 6 ik parameters)
# it just needs the 2 parameters used to determine the ground contact
recomputed_hexapod = recompute_hexapod(dimensions, ik_parameters, poses)

# in theory, they should be the roughly the same point
assert recomputed_hexapod == updated_hexapod
```

> # First function: `inverse_kinematics_update()`

Roughly the algorithm of the `inverse_kinematics_update()` is like this:
Since we know and can compute for the body contact(`p0`) and the ground contact(`p3`)
and we know the three lengths of the limbs (`coxia`, `femur`, `tibia`). And we know that
the first limb is in the same axis as the body contact,
we can solve for the 2 points of the legs (the coxia joint (`p1`), femur joint(`p2`))
and we can compute the 3 angles with trigonometry
```
|--coxia-----\ ----
p0 ------- p1 \    \
               \ femur
                \    \
                 p2 ---
                /    /
               /   tibia
              /    /
            p3 -------

point p0 - known
point p3 - known
coxia length - known
femur length - known
tibia length - known
given p0 and p1 lie on the same known axis

solve for
p1
p2

We can also solve for the angles these points make..

We return the hexapod with the updated points
and also the 18 computed angles called "poses"
```

> # Second function `recompute_hexapod()`

```python

def recompute_hexapod(dimensions, ik_parameters, poses):
    # ❗❗IMPORTANT!This assumes leg with id 3 and id 4 are on the ground
    # THIS IS NOT ALWAYS THE CASE.
    # Should check which two legs are both on the ground before and after
    # instead of using leg 3 and 4'

    # make the hexapod with all angles = 0
    # update the hexapod so that we know which given points are in contact with the ground
    old_hexapod = VirtualHexapod(dimensions)
    old_hexapod.update_stance(ik_parameters["hip_stance"], ik_parameters["leg_stance"])

    # get two of those points that contact the ground
    # the hexapod we must end up with must be stepping in these points
    old_p1 = deepcopy(old_hexapod.legs[3].p3)
    old_p2 = deepcopy(old_hexapod.legs[4].p3)
    old_vector = vector_from_to(old_p1, old_p2)

    # make a new hexapod with all angles = 0
    # and update given the poses/ angles we've computed
    new_hexapod = VirtualHexapod(dimensions)
    new_hexapod.update(poses)
    # get two of those points
    new_p1 = deepcopy(new_hexapod.legs[3].p3)
    new_p2 = deepcopy(new_hexapod.legs[4].p3)
    new_vector = vector_from_to(new_p1, new_p2)

    # we must translate and rotate the hexapod with the pose
    # so that the hexapod is stepping on the predefined ground contact points
    translate_vector = vector_from_to(new_p1, old_p1)
    _, twist_frame = find_twist_to_recompute_hexapod(new_vector, old_vector)
    new_hexapod.rotate_and_shift(twist_frame, 0)
    twisted_p2 = new_hexapod.legs[4].p3
    translate_vector = vector_from_to(twisted_p2, old_p2)
    new_hexapod.move_xyz(translate_vector.x, translate_vector.y, 0)

    # These things should be true
    assert np.isclose(new_p1.z, 0)
    assert np.isclose(new_p2.z, 0)
    assert np.isclose(old_p1.z, 0, atol=0.1)
    assert np.isclose(old_p2.z, 0, atol=0.1)
    assert new_p1.name == old_p1.name
    assert new_p2.name == old_p2.name
    assert np.isclose(length(new_vector), length(old_vector), atol=0.1)

    return new_hexapod
```

# My ideas on how to debug

I don't know if this is just a floating point error that gets amplified over time.
I don't know yet, but how do I try debug this?

1. Given the computed angles /poses, use the kinematics page
`https://hexapod-robot-simulator.herokuapp.com/kinematics` to see if they
match either the `updated_hexapod` or the `recomputed_hexapod`


2. Given the hexapod points computed at the function `inverse_kinematics_update()`
double check whether the computed angles `alpha`, `beta` and `gamma` for each of the
6 legs match when we get the computed `p0`, `p1`, `p2` and `p3` for each leg.

3. We compute the points of `recomputed_hexapod` given the 18 angles (`old_pose`). Using
these points, we compute the 18 angles (`new_pose`) and see if they match
`assert new_pose == old_pose`, so in essence,

```
1. Given 18 angles (old_poses) solve for computed hexapod points
2. Given computed hexapod points so;ve for 18 angles (new_poses)

assert new_poses == old_poses
```

Here are some screenshots of the problem

# Part 1: Output as expected
Here's one where the `updated_hexapod` and the `recomputed_hexapod` are the same.

> ### 1. `recomputed_hexapod`
![](/robotics-blog/darkmode-local-12-0.png)

> ### 2. `updated_hexapod`
![](/robotics-blog/darkmode-heroku-12-0.png)

# Part 2: Output not as expected
Here's one case where they aren't

> ## 1. `recomputed_hexapod`
![](/robotics-blog/darkmode-local-13-5.png)

> ## 2. `updated_hexapod`
![](/robotics-blog/darkmode-heroku-13-5.png)

# Part 3: A closer look, observations

As you can see here, both cases are a bit weird.
One observation is that on both cases, not all
the feet are in contact with the ground. Pay attention to the highlighted points.

## 1. `recomputed_hexapod`
![](/robotics-blog/hexapod-local.png)

## 2. `updated_hexapod`
![](/robotics-blog/hexapod-heroku.png)

In one of the cases one of the feet are shoved below the ground.
On the other case, some of the feet are above the ground.
These things should actually not happen, we should be alerted by
the `inverse_kinematics_update`

> Inspect the full code in these links
> - https://github.com/mithi/hexapod-robot-simulator/blob/master/hexapod/ik_solver/ik_solver.py
> - https://github.com/mithi/hexapod-robot-simulator/blob/master/hexapod/ik_solver/helpers.py

# Part 4: Videos

Here's a few videos showing the two cases. Pay attention to the url
`localhost` is the one with `recomputed_hexapod()` while `heroku` is the one with
`inverse_kinematics_update`


{{< rawhtml >}}


<video width="640" height="480" controls>
  <source src="/robotics-blog/hexapod1.mp4" type="video/mp4">
Your browser does not support the video tag.
</video>

<br>

<video width="640" height="480" controls>
  <source src="/robotics-blog/hexapod2.mp4" type="video/mp4">
Your browser does not support the video tag.
</video>

{{< /rawhtml >}}

Still in the process of debugging. Any ideas is much appreciated.
