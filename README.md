### Options
This workflow has additional options that you can use to customize it for your use case, following are the list of options available:

| Option | Default Value | Description | Required |
|--------|--------|--------|--------|
| `feed_list` | `""` | Comma separated list of RSS feed urls, eg: `https://example1.com,https://example2.com` | Yes |
| `max_post_count` | `5` | Maximum number of posts you want to show on your readme, all feeds combined | No  |
| `readme_path` | `./README.md` | Path of the readme file you want to update | No |
| `gh_token` | your github token with repo scope | Use this to configure the token of the user that commits the workflow result to GitHub | No |
| `comment_tag_name` | `BLOG-POST-LIST` | Allows you to override the default comment tag name (`<!-- BLOG-POST-LIST:START --><!-- BLOG-POST-LIST:END -->`), if you want to show multiple instances of the action on the same repo, see advanced usage for more info | No | 
| `disable_sort` | `false` | Disables the sorting of list based on publish date | No |
| `template` | `default` | Allows you to change the structure of the posts list by using the `$title`, `$url`, `$newline` and `$date` variables. By default this workflow uses markdown list format to render the posts, you can override this behaviour using this option. Eg: `[$title]($url) ` will give you a space separated list of posts | No |
| `date_format` | `UTC:ddd mmm dd yyyy h:MM TT` | Allows you to change the format of the date or time displayed when using the $date in the template option. This uses NPM dateformat library, please read the library [documentation](https://www.npmjs.com/package/dateformat#named-formats) for the supported formats | No |
| `user_agent` | `rss-parser` | Allows you to customize the user agent used by the RSS feed crawler | No |
| `accept_header` | `application/rss+xml` | Allows you to customize the accept header of the http requests | No |
| `tag_post_pre_newline` | `true` if you are not using **template** option | Allows you to insert a newline before the closing tag and after the opening tag when using the template option if needed, for better formatting | No |
| `filter_comments` | `medium,stackoverflow/Comment by $author/,stackexchange/Comment by $author/` | Comma separated list of platforms you want to enable the comment filter. Available filters: <ul><li>`medium`: Allow you to filter out the medium comments</li><li>`stackoverflow/Comment by $author/`: Allow you to filter out the StackOverflow comments. Argument to this filter is optional, it defaults to 'Comment by $author'. If you use any language other than English on StackOverflow, you can use this argument to customize it. See [#16](https://github.com/gautamkrishnar/blog-post-workflow/issues/16) for more info.</li><li>`stackexchange/Comment by $author/`: Allow you to filter out the StackExchange comments. Argument to this filter follows same format as `stackoverflow` filter's argument.</li></ul> | No |
| `commit_message` | `Updated with the latest blog posts` | Allows you to customize the commit message | No |
| `committer_username` | `blog-post-bot` | Allows you to customize the committer username | No |
| `committer_email` | `blog-post-bot@example.com` | Allows you to customize the committer email | No |

### Advanced usage examples
#### StackOverflow example
Following configuration allows you to show your latest StackOverflow activity along with your latest blog posts in the Github profile or project readme:
- Follow the steps mentioned in [how to use](#how-to-use) section
- Add the following section to your **README.md** file, you can give whatever title you want. Just make sure that you use `<!-- STACKOVERFLOW:START --><!-- STACKOVERFLOW:END -->` in your readme. The workflow will replace this comment with the actual StackOverflow activity: 
```markdown
# StackOverflow Activity
<!-- STACKOVERFLOW:START -->
<!-- STACKOVERFLOW:END -->
```
- Create `stack-overflow-workflow.yml` in your `workflows` folder with the following contents, replace **4214976** with your StackOverflow [user id](https://meta.stackexchange.com/questions/98771/what-is-my-user-id/111130#111130):
```yaml
name: Latest stack overflow activity
on:
  schedule:
    # Runs every 5 minutes
    - cron: '*/5 * * * *'

jobs:
  update-readme-with-stack-overflow:
    name: Update this repo's README with latest activity from StackOverflow
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: gautamkrishnar/blog-post-workflow@master
        with:
          comment_tag_name: "STACKOVERFLOW"
          commit_message: "Updated readme with the latest stackOverflow data"
          feed_list: "https://stackoverflow.com/feeds/user/4214976"
```
<details>
  <summary>See the result!</summary>

  ![advanced](https://user-images.githubusercontent.com/8397274/88197889-b727ff80-cc60-11ea-8e4a-b1fbd8dd9d06.png)
</details>

### Popular Sources 
Following are the list of some popular blogging platforms and their RSS feed urls:

| Name | Feed URL | Comments | Example |
|--------|--------|--------|--------|
| [Dev.to](https://dev.to/) | `https://dev.to/feed/username` | Replace username wih your own username | https://dev.to/feed/gautamkrishnar |
| [Wordpress](https://wordpress.org/) | `https://www.example.com/feed/` | Replace wih your own blog url | https://www.gautamkrishnar.com/feed/ |
| [Medium](https://medium.com/) | `https://medium.com/feed/@username` | Replace @username with your medium username | https://medium.com/feed/@khaosdoctor |
| [Stackoverflow](https://stackoverflow.com/) | `https://stackoverflow.com/feeds/user/userid` | Replace wih your StackOverflow [UserId](https://meta.stackexchange.com/questions/98771/what-is-my-user-id/111130#111130) | https://stackoverflow.com/feeds/user/5283532 |
| [StackExchange](https://stackexchange.com/) | `https://subdomain.stackexchange.com/feeds/user/userid` | Replace wih your StackExchange [UserId](https://meta.stackexchange.com/questions/98771/what-is-my-user-id/111130#111130) and sub-domain | https://devops.stackexchange.com/feeds/user/15 |
| [Ghost](https://ghost.org/) | `https://www.example.com/rss/` | Replace wih your own blog url | https://blog.codinghorror.com/rss/ |
| [Drupal](https://www.drupal.org/) | `https://www.example.com/rss.xml` | Replace wih your own blog url | https://www.arsenal.com/rss.xml |
| [Youtube Playlists](https://www.youtube.com) | `https://www.youtube.com/feeds/videos.xml?playlist_id=playlistId` | Replace `playlistId` with your own Youtube playlist id | https://www.youtube.com/feeds/videos.xml?playlist_id=PLJNqgDLpd5E69Kc664st4j7727sbzyx0X |
| [Youtube Channel Video list](https://www.youtube.com) |  `https://www.youtube.com/feeds/videos.xml?channel_id=channelId` | Replace `channelId` with your own Youtube channel id | https://www.youtube.com/feeds/videos.xml?channel_id=UCDCHcqyeQgJ-jVSd6VJkbCw |
| [Anchor.fm Podcasts](https://anchor.fm/) | `https://anchor.fm/s/podcastId/podcast/rss` | You can get the rss feed url of a podcast by following [these](https://help.anchor.fm/hc/en-us/articles/360027712351-Locating-your-Anchor-RSS-feed) instructions | https://anchor.fm/s/1e784a38/podcast/rss |
| [Hashnode](https://hashnode.com/) | `https://@username.hashnode.dev/rss.xml` | Replace @username with your hasnode username | https://polilluminato.hashnode.dev/rss.xml |

### Examples 
* [My own GitHub profile readme](https://github.com/gautamkrishnar) - [YML File](https://github.com/gautamkrishnar/gautamkrishnar/blob/master/.github/workflows/blog-post-workflow.yml)
* [Lucas Santos' GitHub profile readme](https://github.com/khaosdoctor) - [YML File](https://github.com/khaosdoctor/khaosdoctor/blob/main/.github/workflows/update-blog-posts.yml)

### Demo video
Please see the [video](https://www.youtube.com/watch?v=ECuqb5Tv9qI) by [@codeSTACKr](https://github.com/codeSTACKr).

### Contributing
Please see [CONTRIBUTING.md](CONTRIBUTING.md) for getting started with the contribution. Make sure that you follow [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md) while contributing and engaging in the discussions. **When contributing, please first discuss the change you wish to make via an issue on this repository before making the actual change**.

#### ToDo
- [ ] Add more sources
- [ ] Fix bugs if any

### Bugs
If you are experiencing any bugs, don???t forget to open a [new issue](https://github.com/gautamkrishnar/blog-post-workflow/issues/new).

### Thanks
- Thanks to all the existing users of this workflow
- Thanks to all the [contributors](https://github.com/gautamkrishnar/blog-post-workflow/graphs/contributors)
- Thanks to [@codeSTACKr](https://github.com/codeSTACKr) for [this](https://www.youtube.com/watch?v=ECuqb5Tv9qI) amazing video

### Liked it?
Hope you liked this project, don't forget to give it a star ???
