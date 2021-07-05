# GoGettr
GoGettr is an extraction tool for GETTR, a "non-bias [sic] social network." (We will not reward their domain with a hyperlink.)

This tool does not currently require any authentication with GETTR; it gathers all its data through publicly accessible endpoints. Our plan is to keep it this way for as long as possible.

Currently, this tool can:

* Pull all the posts made on the platform
* Pull all the posts of a user on the platform

## Robustness

This tool was made by "reverse engineering" GETTR's API. (It wasn't that hard.) Because we have no insight into GETTR's internals, there's no guarantee that this tool provides an exhaustive or reliable export of GETTR content. Still, it does a pretty good job.

Note that GoGettr does barely any formatting or postprocessing of the data returned by the API. That means that if Gettr changes the schema of the data that they return, then so will GoGettr. Don't assume that the structure of GoGettr's responses will remain constant.

## CLI Playbook

#### Pull all posts (starting at id 1)

```
gogettr all
```

#### Pull all comments

```
gogettr all --type comments
```

#### Pull all posts (starting at a particular ID and moving backward through IDs)

```
gogettr all-posts --rev --last pay8d
```

#### Pull all posts from a user

```
gogettr user USERNAME --type posts
```

#### Pull all comments from a user

```
gogettr user USERNAME --type comments
```

#### Pull all likes from a user

```
gogettr user USERNAME --type likes
```

#### Pull a user's information

```
gogettr user-info USERNAME
```

## CLI Usage

```
Usage: gogettr [OPTIONS] COMMAND [ARGS]...

Options:
  --help  Show this message and exit.

Commands:
  all             Pull all posts (or comments) sequentially.
  hashtags        Pull the suggested hashtags (the top suggestions are...
  suggested       Pull the suggested users (users displayed on the home...
  trends          Pull all the trends (posts displayed on the home page).
  user            Pull the posts, likes, or comments made by a user.
  user-followers  Pull all a user's followers.
  user-following  Pull all users a given user follows.
  user-info       Pull given user's information.
```

### `all`

```
Usage: gogettr all [OPTIONS]

  Pull all posts (or comments) sequentially.

Options:
  --first TEXT             the ID of the first post to pull
  --last TEXT              the ID of the last post to pull
  --max INTEGER            the maximum number of posts to pull
  --rev                    increment reverse chronologically (i.e., from last
                           to first)
  --type [posts|comments]
  --help                   Show this message and exit.
```

### `hashtags`

```
Usage: gogettr hashtags [OPTIONS]

  Pull the suggested hashtags (the top suggestions are displayed on the front
  page).

  Note that while the first five or so hashtags have expanded information
  associated with them, later results do not.

Options:
  --max INTEGER  the maximum number of hashtags to pull
  --help         Show this message and exit.
```

### `suggested`

```
Usage: gogettr suggested [OPTIONS]

  Pull the suggested users (users displayed on the home page).

Options:
  --max INTEGER  the maximum number of users to pull
  --help         Show this message and exit.
```

### `trends`

Usage: gogettr trends [OPTIONS]

  Pull all the trends (posts displayed on the home page).

Options:
  --max INTEGER  the maximum number of posts to pull
  --until TEXT   the ID of the earliest post to pull
  --help         Show this message and exit.

### `user`

```
Usage: gogettr user [OPTIONS] USERNAME

  Pull the posts, likes, or comments made by a user.

Options:
  --max INTEGER                  the maximum number of activities to pull
  --until TEXT                   the ID of the earliest activity to pull for
                                 the user
  --type [posts|comments|likes]
  --help                         Show this message and exit.
```

### `user-followers`

```
Usage: gogettr user-followers [OPTIONS] USERNAME

  Pull all a user's followers.

Options:
  --max INTEGER  the maximum number of users to pull
  --help         Show this message and exit.
```

### `user-following`

```
Usage: gogettr user-following [OPTIONS] USERNAME

  Pull all users a given user follows.

Options:
  --max INTEGER  the maximum number of users to pull
  --help         Show this message and exit.
```

### `user-info`

```
Usage: gogettr user-info [OPTIONS] USERNAME

  Pull given user's information.

Options:
  --help  Show this message and exit.
```

## Module Usage

You can use GoGettr as a Python module. For example, here's how you would pull all a user's posts:

```python
from gogettr import PublicClient
client = PublicClient()
posts = client.user_activity(username="support", type="posts")
```

For more examples of using GoGettr as a module, check out the [tests directory](tests/). Note that the API surface can't be considered quite stable yet. In the case that Gettr changes their API, we may refactor our API to match. We'll do our best to make as few public-facing API changes as possible, however.

We've organized GoGettr to group related API functionality into the same capabilities; for example, pulling users' comments, posts, and likes is all done by the same function (inside `user_activity.py`), and pulling followers and following is done by the same function (inside `user_relationships.py`). That means there isn't perfect correspondence between the CLI surface and the API surface.

## Development

To run gogettr in a development environment, you'll need [Poetry](https://python-poetry.org). Install the dependencies by running `poetry install`, and then you're all set to work on gogettr locally.

To run the tests, run `poetry run pytest`.

To access the CLI, run `poetry run gogettr`.

## Wishlist

We hope to add support for the following capabilities to GoGettr:

- [ ] Pull all comments for a post
- [ ] Pull all users on the platform
- [ ] Search for content
- [ ] Multithreaded/concurrent API requests for sequential scans (e.g., pulling all posts on the platform)
