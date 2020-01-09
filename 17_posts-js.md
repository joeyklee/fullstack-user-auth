# Step 17: Adding functionality to render, add, edit, and delete posts


Unless there's demand from the community, I think for now, I will stick to a high-level overview of the `Posts` class. Since the main focus of this tutorial is the auth functionality, I won't go into detail about how I've chosen to update the UI views. 

In this step you are going to write the JavaScript to:
* GET data from your `/api/v1/posts`
* render that data to your page under the header that says `all posts`.
* handle the events related to submitting a new post 
* handle the events related to submitting edits to an existing post

The major thing to note here is that the PUT, PUT, and DELETE requests are all using the the `auth_token` cookie that is planted in your browser when you either "signup" or "log-in". This is what allows your server to go through the `auth` and `isOwner` middleware. 


```js
class Posts {
  constructor(){
    // posts data
    this.posts = [];
    // posts base api url
    this.postsURL = "http://localhost:3030/api/v1/posts";
    // query selectors
    this.postsList = document.querySelector('.posts__view__list');
    this.postEditForm = document.querySelector('.post__form-edit');
    this.postNewForm = document.querySelector('.post__form-new');

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
          title: this.postEditForm.title.value,
          link: this.postEditForm.link.value,
          description: this.postEditForm.description.value,
          id: this.postEditForm.id.value,
        }
        await this.handleEditPost(editData);  
        await this.updateView();
      } catch (error) {
        alert(error);
      }
    });

    // handle submitting data from the new form
    this.postNewForm.addEventListener('submit', async e => {
      try {
        e.preventDefault();

        const newData = {
          title: this.postNewForm.title.value,
          link: this.postNewForm.link.value,
          description: this.postNewForm.description.value,
        }
        await this.handleNewPost(newData);  
        await this.updateView();
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
      this.posts = data.reverse();
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
        method: 'DELETE',
        credentials: "same-origin",
      }
      const deleteUrl = `${this.postsURL}/${id}`
      let data = await fetch(deleteUrl, options)
      data = await data.json();
      await this.updateView();
      alert(data.message);
    } catch (err) {
      console.log(err)
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
        headers: {
          'Accept': 'application/json',
          'Content-Type': 'application/json'
        },
        credentials: "same-origin",
        body: JSON.stringify(formData)
      }
      const editUrl = `${this.postsURL}/${formData.id}`
      let data = await fetch(editUrl, options)
      data = await data.json();

      alert('post successfully updated');
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
        headers: {
          'Accept': 'application/json',
          'Content-Type': 'application/json'
        },
        credentials: "include",
        body: JSON.stringify(formData)
      }
      let data = await fetch(this.postsURL, options)
      data = await data.json();
      console.log(data);
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
    output.classList.add('posts__view__list__item');
    output.innerHTML = `
      <div class="posts__view__list__item__functions">
        <button class="posts__view__list__item__functions__button posts__view__list__item__functions__button--edit" data-id="${post._id}">edit</button>
        <button class="posts__view__list__item__functions__button posts__view__list__item__functions__button--delete" data-id="${post._id}">delete</button>
      </div>
      <h4 class="posts__view__list__item__title">
        <a href="${post.link}" target="_blank" rel="noreferrer" 
          class="posts__view__list__item__title-link">
          ${post.title}
        </a>
      </h4>
      <p class="posts__view__list__item__description">
        ${post.description}
      </p>
      <ul class="posts__view__list__item__meta-list">
        <li class="posts__view__list__item__meta-list__item">Submitted by: ${post.createdBy_username}</li>
        <li class="posts__view__list__item__meta-list__item">
          <a class="posts__view__list__item__meta-list__item-link" href="${post.link}">${post.link}</a>
        </li>
      </ul>
    `;

    // listen for delete events
    const deleteButton = output.querySelector('.posts__view__list__item__functions__button--delete');
    deleteButton.addEventListener('click', async e =>{
      await this.handleDeletePost(e.target.dataset.id)
    });
    // listen for toggle events
    const editButton = output.querySelector('.posts__view__list__item__functions__button--edit');
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

## Add everything to `public/js/main.js`

The last thing to do is to add everything to `public/js/main.js`:

In `public/js/main.js`:
```js
window.addEventListener("DOMContentLoaded", function(){
  console.log('hello lovely human');
  const posts = new Posts();
  const login = new Login();
  const signup = new Signup();
});
```


## Add and Commit your changes

```
git add .
git commit -m "adds all functions to main"
```

## Congratulations! 🎉

You've now completed the tutorial. If you'd like to read the recap and conclusion continue onto the next step:
* ↳ [Step 18: Recap and conclusions](/18_conclusion.md)

