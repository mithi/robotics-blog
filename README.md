# Mithi's Robotics Blog

Make a post
```
hugo new blog/your-subdirectory/my-post.md
```

You'd find it in  `content/blog/your-subdirectory/`


If you're going to put images,
put it in a new subdirectory `static/images/your-subdirectory/`


and then in `cargo.toml` don't forget to add

```
staticDir = ["static", "static/images", "static/images/your-subdirectory"]
```

After writing your post, start the server

```
hugo server -D
```

Build

```
hugo -D
```

Push to master.

```
git push
```

Check it out in https://mithi.github.io/robotics-blog
If renders properly
