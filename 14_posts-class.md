# Step 14: Posts Class

In this step you are going to write the JavaScript to:
* GET data from your `/api/v1/posts`
* render that data to your page under the header that says `all posts`.
* handle the events related to submitting a new post 
* handle the events related to submitting edits to an existing post

## Roadmap:


## Imposing some structure

There are many ways of handling front end JavaScript code, so I don't want to go overboard, but I do want to impose some structure so that we can separate out our concerns in a manner that is organized but not impossible to decode (sometimes over engineering makes things unweildy IMHO!).

I will be using JavaScript Classes to encapsulate the functionality for our various `forms` and features. 

## Add the reference to our Posts class 

The Posts class will be used to encapsulate all of the functionality and data we need to show our posts, create new posts, edit and delete existing posts. 

First thing's first. Add a reference in your `public/index.html` to the `public/js/Posts.js`  class we are about to create:


In `public/js/index.html`, at the bottom:
```html
<!-- JS Scripts -->
<script src="js/Posts.js"></script>

```

## Creating the Posts class: `class Posts`

Create a file called `public/js/Posts.js`. This will be our Posts class.

Because this is a very long code snippet, please read the code comments for details about each of the functions. 

In `public/js/Posts.js`:

```js
class Posts {
  constructor(){
    // posts data
    this.posts = [];
    // posts base api url
    this.postsURL = "http://localhost:3030/api/v1/posts";
    // query selectors
    this.postsList = document.querySelector('.section__view__posts');
    this.postEditForm = document.querySelector('.link__form--edit');
    this.postNewForm = document.querySelector('.link__form--new');

    // initialize the event listeners
    this.init();
  }
  
  /**
   * initialzie with event listeners
   */
  init(){
    this.initEventListeners();
    // update the view on initialization
    this.updateView();
  }

  /**
   * initializeEventListeners
   */
  initEventListeners(){
    // handling submitting data from the edit form
    this.postEditForm.addEventListener('submit', async e => {
      try {
        e.preventDefault();

        const editData = {
          title: this.postEditForm.title,
          link: this.postEditForm.link,
          description: this.postEditForm.description,
        }
        await this.handleEditPost(editData);  
        await updateView();
      } catch (error) {
        alert(error);
      }
    });

    // handle submitting data from the new form
    this.postNewForm.addEventListener('submit', async e => {
      try {
        e.preventDefault();

        const newData = {
          title: this.postNewForm.title,
          link: this.postNewForm.link,
          description: this.postNewForm.description,
        }
        await this.handleNewPost(newData);  
        await updateView();
      } catch (error) {
        alert(error);
      }
    });
  }

  /**
   * update the view by requesting all the posts 
   * and creating the post list again
   */
  async updateView(){
    await this.getPosts();
    this.createPostList();
  }

  /**
   * GET posts using fetch
   */
  async getPosts(){
    try{
      let data = await fetch(this.postsURL);
      data = await data.json();
      this.posts = data;
      return data;
    }catch(err){
      throw new Error(err);
    }
  }

  /**
   * Handle deleting the psot based on the id
   * @param {*} id 
   */
  async handleDeletePost(id){
    try {
      const options = {
        method: 'POST',
        credentials: "same-origin"
      }
      const deleteUrl = `${this.postsURL}/${id}`
      let data = await fetch(deleteUrl, options)
      data = await data.json();

      alert(data.message);
    } catch (err) {
      alert(err);
      throw new Error(err);
    }
  }

  /**
   * Toggle edit post
   */
  setEditPost(id){
    const postEditing = this.posts.find(item => item._id === id)
    this.postEditForm.title.value = postEditing.title;
    this.postEditForm.description.value = postEditing.description;
    this.postEditForm.id.value = postEditing._id;
    this.postEditForm.link.value = postEditing.link;
  }

  /**
   * handling submitting the post data edits
   */
  async handleEditPost(formData){
    try {
      const options = {
        method: 'PUT',
        credentials: "same-origin",
        body: JSON.stringify(formData)
      }
      const editUrl = `${this.postsUrl}/${id}`
      let data = await fetch(editUrl, options)
      data = await data.json();

      alert(data.message);
    } catch (err) {
      alert(err);
      throw new Error(err);
    }
  }

  /**
   * handle a new post submission
   * @param {*} formData 
   */
  async handleNewPost(formData){
    try {
      const options = {
        method: 'POST',
        credentials: "same-origin",
        body: JSON.stringify(formData)
      }
      const editUrl = `${this.postsUrl}`
      let data = await fetch(editUrl, options)
      data = await data.json();

      alert(data.message);
    } catch (err) {
      alert(err);
      throw new Error(err);
    }
  }

  /**
   * Create one post item
   * @param {*} post 
   */
  createPostItem(post){
    const output = document.createElement('li');
    output.classList.add('section__view__post');
    output.innerHTML = `
      <div class="section__view__post__functions">
        <button class="section__view__post__button section__view__post__button--edit" data-id="${post._id}">edit</button>
        <button class="section__view__post__button section__view__post__button--delete" data-id="${post._id}">delete</button>
      </div>
      <h4 class="section__view__post__title">
        <a href="${post.link}" target="_blank" rel="noreferrer" 
          class="section__view__post__link section__view__post__link--large">
          ${post.title}
        </a>
      </h4>
      <p class="section__view__post__description">
        ${post.description}
      </p>
      <ul class="section__view__post__meta">
        <li class="section__view__post__meta__section section__view__post__meta__username">Submitted by: ${post.createdBy_username}</li>
        <li class="section__view__post__meta__section section__view__post__meta__link">
          <a href="${post.link}" class="section__view__post__link--small">${post.link}</a>
        </li>
      </ul>
    `;

    // listen for delete events
    const deleteButton = output.querySelector('.section__view__post__button--delete');
    deleteButton.addEventListener('click', async e =>{
      await this.handleDeletePost(e.target.dataset.id)
    });
    // listen for toggle events
    const editButton = output.querySelector('.section__view__post__button--edit');
    editButton.addEventListener('click', async e =>{
      this.setEditPost(e.target.dataset.id)
    });
    
    return output;
  }

  /**
   * Create an array of post items
   * @param {*} posts 
   */
  createPostList(){
    const posts = this.posts.map(item => this.createPostItem(item));
    // remove all the posts
    this.postsList.innerHTML = "";
    // append the posts to the postList
    posts.forEach(item => {
      this.postsList.appendChild(item);
    });
  }

}
```

## Add posts functionality to your `public/js/main.js`

Add your `Posts` class functionality to the `public/js/main.js`

```js
window.addEventListener("DOMContentLoaded", function(){
  console.log('hello lovely human');

  const main = async function(){
    const posts = new Posts();

  }

  main();
});
```

You'll now see:

![Screenshot of the the Posts class rendering the view correctly](/assets/14_posts-class-01.png)


## Add and Commit your changes

```
git add .
git commit -m "adds Posts class"
```

***
***
***
## Rest Stop:

Whew! There's a lot in there, but the general idea is that it allows you to:
* submit a new post
* edit an existing post
* delete an existing post
* update/re-render the view

***
***
***