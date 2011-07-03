---
title: Time.now in AR model specs is evil

location: Liverpool, England

layout: default-post

---

If you're stubbing `Time.now` based on some value of `Time.now` in an ActiveRecord model spec, then saving before you assert equality, beware. `Time.now` is accurate to milliseconds, but the database is not. Always use `Time.parse` with a string to ensure you're only accurate to the second in your stubbed value for comparison.