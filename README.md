# how-coroutine-works-like-a-kitchen
How coroutine works like Cooks in a kitchen

**"The Restaurant of Cooks, the Magic Recipe Notes, and the Cook Manager"**

In the grand kitchen of your app, there’s a **team of Cooks** — not just one — each skills at different stations: Grill Station, Salad Station, Pastry Station (just like threads: Main, IO, Default...).

When a new dish order (a coroutine) comes in, a **Cook** (thread) is picked from the kitchen.

Before starting, a **Cook Manager** (ContinuationInterceptor) is assigned to oversee the work.

The Manager holds a notebook and watches over all the Cooks.

The Cook starts working on the dish (executing code in the coroutine block).

At some point, the Cook needs to *wait* — maybe for the dough to rise, the water to boil, or a delivery to arrive (suspend point like `delay()`, `await()`, `network call`).

Instead of wasting time standing around, the Cook:

- **Writes down all the steps needed to continue** — ingredients prepped, timing, sauces mixed, everything (this is the **Continuation**).
- Asks another Cook (Cook 2) to help him finish some work, like boiling the water (`delay`), or get a fried egg (`network call`).
- **Hands the Magic Recipe Note (`code`) and all the prepared ingredients (`variables`)** to the **Cook Manager (wraps the Continuation as `DispatchedContinuation` and transfer it to ContinuationInterceptor)**.
- **Leaves to work on something else** (non-blocking of this Cook, freeing the thread for other tasks).

Meanwhile, another Cook (Cook2) handles the boiling water or watches the dough rise, or fry an egg and hand it in.

When the waiting is done (event completed), Cook 2 notifies the **Cook Manager** the job is done, he can resume with the next steps.

So the Cook Manager (CoroutineInterceptor) steps in with the Magic Recipe Note and the prepared ingredients (the `DispatchedContinuation`):

- Decides: **"Who should pick up this dish and continue cooking?"**
    - Sometimes it must be **the same Cook** (e.g., updating UI on Main thread).
    - Sometimes **any available Cook** can continue (IO or worker thread).

The Manager might:

- Hand the note back to the original Cook if needed.
- Or choose a different Cook (another thread) if it's better.

The selected Cook resumes *exactly where the previous left off*, thanks to the Magic Note.

Result: the dish (suspend function) gets completed seamlessly without any kitchen blockages (no blocking threads)!

At anytime, any Cook will notify Cook Manager (ContinuationInterceptor) to resume with result or with exception when he finishes.

ContinuationInterceptor knows how to resume.

## Note:
All rights reserved. No part of this may be distributed without permission.
