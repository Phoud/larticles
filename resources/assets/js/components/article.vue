<template>
	<div>
		<h2>Articles</h2>
		<form @submit.prevent="addArticle" class="mb-3">
			<div class="form-group">
				<input class="form-control" type="text" placeholder="Title" v-model="article.title">
			</div>
			<div class="form-group">
				<textarea class="form-control" type="text" placeholder="Body" v-model="article.body"></textarea>
			</div>
			<button type="submit" class="btn btn-primary">Save</button>
		</form>
		<nav aria-label="Page navigation example">
			<ul class="pagination">
				<li v-bind:class="[{disabled: !pagination.prev_page_url}]" class="page-item"><a class="page-link" href="#" @click="fetchArticles(pagination.prev_page_url)">Previous</a></li>
				
				<li class="page-item disabled"><a class="page-link text-dark" href="#">Page {{pagination.current_page}} of {{pagination.last_page}}</a></li>

				<li v-bind:class="[{disabled: !pagination.next_page_url}]" class="page-item"><a class="page-link" href="#" @click="fetchArticles(pagination.next_page_url)">Next</a></li>

				<li v-bind:class="[{disabled: !pagination.last_page}]" class="page-item"><a class="page-link" href="#" @click="fetchArticles(pagination.last_page)">Last Page</a></li>
			</ul>
		</nav>
		<div class="card card-body mb-3" v-for="article in articles" v-bind:key="article.id">
			<h4>{{article.title}}</h4>
			<p>{{article.body}}</p>
			<hr>
			<button @click="deleteInfo(article.id)" class="btn btn-danger mb-2">Delete</button>
			<button @click="updateInfo(article)" class="btn btn-warning">Update</button>
		</div>
	</div>

</template>

<script>
export default{
	data(){
		return {
			articles: [],
			article:{
				'id':'',
				'title': '',
				'body':''

			},
			article_id: '',
			pagination:{},
			edit: false
		}

	},
	created(){
		this.fetchArticles();

	},

	methods:{
		fetchArticles(page_url){
			let vm = this;
			page_url = page_url || 'api/articles';
			fetch(page_url)
			.then(res => res.json())
			.then(res => {
				this.articles = res.data;
				vm.makePagination(res.meta, res.links);
			})
			.catch(err => console.log(err));

		},

		addArticle(){
			if(this.edit === false){
				fetch('api/articlepost', {

					method: 'post',
					body: JSON.stringify(this.article),
					headers: {
						'content-type': 'application/json'
					}
				
				})
				.then(res => json())
				.then(data => {

					this.article.title = '';
					this.article.body = '';
					
					this.fetchArticles();
				})
				.catch(err => console.log(err));
				
			}else{
				fetch('api/article', {
					method: 'put',
					body: JSON.stringify(this.article),
					headers: {
						'content-type': 'application/json'
					}
				})
				.then(res => json())
				.then(data => {
					this.article.title = '';
					this.article.body = '';
					alert('Article Updated!');
					this.fetchArticles();
				})
				.catch(err => console.log(err));
				
			}
		},
		updateInfo(article){
			this.edit = true;
			this.article.id = article.id;
			this.article.article_id = article.id;
			this.article.title = article.title;
			this.article.body = article.body;

		},
		deleteInfo(id){
			if(confirm('Are you sure you want to delete?')){
				fetch(`api/article/${id}`, {
					method: 'delete'
				})
				.then(res => res.json())
				.then(data => {
					alert('Article removed!');
					this.fetchArticles();
				})
				.catch(err => console.log(err));
			}
		},
		makePagination(meta, links){
			let pagination = {
				current_page: meta.current_page,
				last_page: meta.last_page,
				next_page_url: links.next,
				prev_page_url: links.prev
			}
			this.pagination = pagination;
		}
	}

};
</script>