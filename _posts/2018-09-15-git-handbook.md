---
layout: post
title: Git Handbook
---

## Purpose

Due to the most of time on my job, I need to type many git command lines. So that this article is used to sort several git commands frequently used for easily searching.

## Intorduction

Git is one of a distributed version control system (DVCS) commonly used for open source and commercial software development with significant benefit for individuals, teams and businesses. DVCS allows full access to every files, branches, and interation of project. Unlike once popular centralized version control system, such as perforce, DVCS don't need a constant connection to a central repository. Developers can work anywhere and collaborate asynchronously from any time zone.

## Basic Git Commands

### Set your name and email in Git

Before working with Git on the command line, there are some basic configurations need to be set in advance.  

By replacing {YOUR NAME} with your first and last name:

<div class="language-shell highlighter-rouge"><pre class="highlight"><code><span class="nv">$ </span><span class="nb">git config </span> --global user.name {YOUR NAME} </code></pre></div>

Then, replaceing {EMAIL} with the email associated with your GitHub account:

<div class="language-shell highlighter-rouge"><pre class="highlight"><code><span class="nv">$ </span><span class="nb">git config </span>--global user.email {EMAIL}</code></pre></div>

Now, you can see your current configurations, type:

<div class="language-shell highlighter-rouge"><pre class="highlight"><code><span class="nv">$ </span><span class="nb">git config </span>--list
</code></pre></div>

### High frequency Git commands

<ol>
<li> Show the status of the files on your branch:</li>

<div class="language-shell highlighter-rouge"><pre class="highlight"><code><span class="nv">$ </span><span class="nb">git status </span></code></pre></div>

<li> Create a new branch:</li>

<div class="language-shell highlighter-rouge"><pre class="highlight"><code><span class="nv">$ </span><span class="nb">git branch </span>{BRANCH-NAME}</code></pre></div>

<li> Check out to your new branch:</li>

<div class="language-shell highlighter-rouge"><pre class="highlight"><code><span class="nv">$ </span><span class="nb">git checkout </span>{BRANCH-NAME}</code></pre></div>

<li> Join two or more development histories together:</li>

<div class="language-shell highlighter-rouge"><pre class="highlight"><code><span class="nv">$ </span><span class="nb">git merge </span>{BRANCH-NAME}</code></pre></div>

Example: First, let's check out to the "master" branch, type:

<div class="language-shell highlighter-rouge"><pre class="highlight"><code><span class="nv">$ </span><span class="nb">git checkout </span>master</code></pre></div>

Then, type:

<div class="language-shell highlighter-rouge"><pre class="highlight"><code><span class="nv">$ </span><span class="nb">git merge </span>Dev</code></pre></div>

These two syntaxes will replay the changes made on the "Dev" into the current branch "master" since "Dev" is diverged from "master".

Now, you can push a new commit describing the merging change, and hence the current commit is already on the top of "master". 

<li>Compare the changes</li>

Show you any uncommitted changes since the last commit, type:
<div class="language-shell highlighter-rouge"><pre class="highlight"><code><span class="nv">$ </span><span class="nb">git diff </span></code></pre></div>

Compare a specific file across branches, type:
<div class="language-shell highlighter-rouge"><pre class="highlight"><code><span class="nv">$ </span><span class="nb">git diff </span> {BRANCH-NAME} {OTHER-BRANCH-NAME} {FILE-NAME} </code></pre></div>


<li> Add your file to the staging area:</li>

<div class="language-shell highlighter-rouge"><pre class="highlight"><code><span class="nv">$ </span><span class="nb">git add </span>{FILE-NAME}</code></pre></div>

This is prepared to become part of the next commit.

<li>See the histroy of commits</li>

<div class="language-shell highlighter-rouge"><pre class="highlight"><code><span class="nv">$ </span><span class="nb">git log </span></code></pre></div>

<li>Commit your file and type the commit message </li>

<div class="language-shell highlighter-rouge"><pre class="highlight"><code><span class="nv">$ </span><span class="nb">git commit </span></code> -m "your message"</pre></div>

<li>Push your commit to the remote and set a trackng branch</li>

<div class="language-shell highlighter-rouge"><pre class="highlight"><code><span class="nv">$ </span><span class="nb">git push </span></code> -u origin {BRANCH-NAME}</pre></div>

<li>Update your local copy of repository:</li>

<div class="language-shell highlighter-rouge"><pre class="highlight"><code><span class="nv">$ </span><span class="nb">git pull </span></code></pre></div>

<li>Stash your work:</li>

========  To be continued   ========

</ol>

## Reference

[1] [Git Handbook](https://guides.github.com/introduction/git-handbook/), GitHub Guides. 

[2] [Learn Git with Bitbucket Cloud](https://www.atlassian.com/git/tutorials/saving-changes/git-stash)
