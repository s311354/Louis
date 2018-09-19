---
layout: post
title: Git Handbook
---

## Purpose

Due to the most of time on my job, I need to type many git command lines. So that this article is used to sort several git commands frequently used for easily searching.

## Intorduction

Git is one of a distributed version control system (DVCS) commonly used for open source and commercial software development with significant benefit for individuals, teams and businesses. DVCS allows full access to every files, branches, and interation of project. Unlike once popular centralized version control system, such as perforce, DVCS don't need a constant connection to a central repository. Developers can work anywhere and collaborate asynchronously from any time zone.

## Basic Git Commands

### Set your Name and Email in Git

Before working with Git on the command line, there are some basic configurations need to be set in advance.  

By replacing {YOUR NAME} with your first and last name:

<div class="language-shell highlighter-rouge"><pre class="highlight"><code><span class="nv">$ </span><span class="nb">git config </span> --global user.name {YOUR NAME} </code></pre></div>

Then, replaceing {EMAIL} with the email associated with your GitHub account:

<div class="language-shell highlighter-rouge"><pre class="highlight"><code><span class="nv">$ </span><span class="nb">git config </span>--global user.email {EMAIL}</code></pre></div>

Now, you can see your current configurations, type:

<div class="language-shell highlighter-rouge"><pre class="highlight"><code><span class="nv">$ </span><span class="nb">git config </span>--list
</code></pre></div>

### High Frequency Git Commands

<h4><a name="TableContent"></a> Table of Contents</h4>

<h6><ol>
    <li><a href="#ShowStatus">Show the status of the files on your branch</a></li>
    <li><a href="#CreateBranch">Create a new branch</a></li>    
    <li><a href="#CheckoutBranch">Check out to your new branch</a></li>
    <li><a href="#MergeBranch">Join two or more development histories together</a></li>
    <li><a href="#CompareChange">Compare the changes</a></li>
    <li><a href="#AddFile">Add your file to the staging area</a></li>
    <li><a href="#HistroyCommit">See the histroy of commits</a></li>
    <li><a href="#CommitFile">Commit your file and type the commit message</a></li>
    <li><a href="#PushCommit">Push your commit to the remote and set a trackng branch</a></li>
    <li><a href="#UpdateRepo">Update your local copy of repository</a></li>
    <li><a href="#StashChange">Stash your work</a></li>
</ol></h6>

#### Git Commands
<ol>
<li><a name="ShowStatus"></a> Show the status of the files on your branch:</li>

<div class="language-shell highlighter-rouge"><pre class="highlight"><code><span class="nv">$ </span><span class="nb">git status </span></code></pre></div>

<li><a name="CreateBranch"></a> Create a new branch:</li>

<div class="language-shell highlighter-rouge"><pre class="highlight"><code><span class="nv">$ </span><span class="nb">git branch </span>{BRANCH-NAME}</code></pre></div>

<li><a name="CheckoutBranch"></a> Check out to your new branch:</li>

<div class="language-shell highlighter-rouge"><pre class="highlight"><code><span class="nv">$ </span><span class="nb">git checkout </span>{BRANCH-NAME}</code></pre></div>

<li><a name="MergeBranch"></a> Join two or more development histories together:</li>

<div class="language-shell highlighter-rouge"><pre class="highlight"><code><span class="nv">$ </span><span class="nb">git merge </span>{BRANCH-NAME}</code></pre></div>

<div>Example:
<div class="language-shell highlighter-rouge"><pre class="highlight">
<code><span class="nv"># </span>First, let's check out to the "master" branch</code>
<code><span class="nv">$ </span><span class="nb">git checkout </span>master</code>
<code><span class="nv"># </span>Then, merge Dev to current branch </code>
<code><span class="nv">$ </span><span class="nb">git merge </span>Dev</code></pre></div>

Description: these two syntaxes will replay the changes made on the "Dev" into the current branch "master" since "Dev" is diverged from "master".

Now, you can push a new commit describing the merging change, and hence the current commit is already on the top of "master".
</div>
<p></p>

<li><a name="CompareChange"></a> Compare the changes:</li>
<p></p>

<ul style="list-style-type:disc">
  <li> Show you any uncommitted changes since the last commit, type:</li>
  <div class="language-shell highlighter-rouge"><pre class="highlight"><code><span class="nv">$ </span><span class="nb">git diff </span></code></pre></div>
  
  <li> Compare a specific file across branches, type:</li>
  <div class="language-shell highlighter-rouge"><pre class="highlight"><code><span class="nv">$ </span><span class="nb">git diff </span> {BRANCH-NAME} {OTHER-BRANCH-NAME} {FILE-NAME} </code></pre></div>
</ul>
  
<li><a name="AddFile"></a>  Add your file to the staging area:</li>

<div class="language-shell highlighter-rouge"><pre class="highlight">
<code><span class="nv"># </span>Preparing to become part of the next commit</code>
<code><span class="nv">$ </span><span class="nb">git add </span>{FILE-NAME}</code></pre></div>

<li><a name="HistroyCommit"></a> See the histroy of commits:</li>

<div class="language-shell highlighter-rouge"><pre class="highlight"><code><span class="nv">$ </span><span class="nb">git log </span></code></pre></div>

<li><a name="CommitFile"></a> Commit your file and type the commit message:</li>

<div class="language-shell highlighter-rouge"><pre class="highlight"><code><span class="nv">$ </span><span class="nb">git commit </span></code> -m "your message"</pre></div>

<li><a name="PushCommit"></a> Push your commit to the remote and set a trackng branch:</li>

<div class="language-shell highlighter-rouge"><pre class="highlight"><code><span class="nv">$ </span><span class="nb">git push </span></code> -u origin {BRANCH-NAME}</pre></div>

<li><a name="UpdateRepo"></a> Update your local copy of repository:</li>

<div class="language-shell highlighter-rouge"><pre class="highlight"><code><span class="nv">$ </span><span class="nb">git pull </span></code></pre></div>

<li><a name="StashChange"></a> Stash your work:</li>
<p></p>

<ul style="list-style-type:disc"> 
  <li> Stash uncommitted changes in local repo </li>
  <div class="language-shell highlighter-rouge"><pre class="highlight"><code><span class="nv">$ </span><span class="nb">git stash </span></code></pre></div>
  
  <li> Re-apply your stashed changes</li>
  <div class="language-shell highlighter-rouge"><pre class="highlight">
  <code><span class="nv"># Reappling the changes and removing</span></code>
  <code><span class="nv">$ </span><span class="nb">git stash pop</span></code>
  <code><span class="nv"># Reappling the changes and keeping</span></code>
  <code><span class="nv">$ </span><span class="nb">git stash apply</span></code></pre></div>
  
  <li> Manage multiple stashes</li>
  <div class="language-shell highlighter-rouge"><pre class="highlight"><code><span class="nv">$ </span><span class="nb">git stash list</span></code></pre></div>
  
  <li> View stash diff</li>
  <div class="language-shell highlighter-rouge"><pre class="highlight"><code><span class="nv">$ </span><span class="nb">git stash show -p</span></code></pre></div>
  
</ul>

</ol>

<a href="#TableContent">Table of Content</a>

## Reference

[1] [Git Handbook](https://guides.github.com/introduction/git-handbook/), GitHub Guides. 

[2] [Git stash](https://www.atlassian.com/git/tutorials/saving-changes/git-stash)

## Note
<p>If you have any constructive criticism or advises, leave the comments below or feel free to email me @qazqazqaz850@gmail.com.

Hope this post will help! :)
</p>


