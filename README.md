# github-new-repo

## Create a Github Repo from the Command Line

Creating a repo from the command line and pushing your commits is definitely faster than going to Github and using the web app to get the job done, but in order to truly make this task speedy, we need some Bash programming. Now you can speed up your Github workflow faster than ever.

## THE BASH FUNCTION

Copy and paster the following function into your `~/.bash_profile`, open a new Terminal window or source `~/.bash_profile`, and the function will be loaded up and ready for use.

Then while in an existing git project, simply type `github-new` and the function will run and create the repo and push your master branch up in one shot. You will need to set some github config variables (instructions will be spit out if you don’t have them).

```
github-new() {
  repo_name=$1

  dir_name=`basename $(pwd)`

  if [ "$repo_name" = "" ]; then
    echo "Repo name (hit enter to use '$dir_name')?"
    read repo_name
  fi

  if [ "$repo_name" = "" ]; then
    repo_name=$dir_name
  fi

  username=`git config github.user`
  if [ "$username" = "" ]; then
    echo "Could not find username, run 'git config --global github.user <username>'"
    invalid_credentials=1
  fi

  token=`git config github.token`
  if [ "$token" = "" ]; then
    echo "Could not find token, run 'git config --global github.token <token>'"
    invalid_credentials=1
  fi

  if [ "$invalid_credentials" == "1" ]; then
    return 1
  fi

  echo -n "Creating Github repository '$repo_name' ..."
  curl -u "YOUR_USER_NAME:YOUR_GITHUB_TOKEN" https://api.github.com/user/repos -d '{"name":"'$repo_name'"}' > /dev/null 2>&1
  echo " done."

  echo -n "Pushing local code to remote ..."
  git remote add origin git@github.com:$username/$repo_name.git > /dev/null 2>&1
  git push -u origin master > /dev/null 2>&1
  echo " done."
}
```

## THE COMMAND

Alternatively, the other option to create a GitHub repository from the command line is to use the straight-forward command line here:

```
curl -u "$username:$token" https://api.github.com/user/repos -d '{"name":"'$repo_name'"}'
```

To use, you simply replace `$username` with your Github username, `$token` with a Personal Access Token for the same user available for generation in your [Github Settings > Applications](https://github.com/settings/applications), and `$repo_name` with your desired new Repository name.
