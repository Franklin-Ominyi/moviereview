<template>
	<main class="content" v-if="!loading && movie">
		<div class="left">
			<h1>{{ movie.Title }}</h1>
			<ul>
				<li><span>Genre:</span> {{ movie.Genre }}</li>
				<li><span>Released:</span> {{ movie.Year }}</li>
				<li><span>Rated:</span> {{ movie.Rated }}</li>
				<li><span>IMDB Rating:</span> {{ movie.imdbRating }}</li>
				<li><span>Director:</span> {{ movie.Director }}</li>
				<li><span>Writer:</span> {{ movie.Writer }}</li>
				<li><span>Actors:</span> {{ movie.Actors }}</li>
			</ul>
			<div>
				<h2>Plot</h2>
				<p style="margin-top: 4px">
					{{ movie.Plot }}
				</p>
			</div>
			<div class="btn-container">
				<button><a :href="imdbLink" target="_blank">Watch Trailer</a></button>
				<button><router-link to="/">Back to Search</router-link></button>
			</div>
		</div>

		<div class="right">
			<img :src="movie.Poster" :alt="movie.Title" />
		</div>
	</main>
	<div v-if="loading" class="loader">
		<i className="fa fa-spinner fa-spin fa-3x" aria-hidden="true"></i>
	</div>
</template>

<script>
import { ref, watchEffect } from "vue";
import axios from "axios";
import swal from "sweetalert";
import router from "@/router";

export default {
	props: ["imdbID"],
	setup(props) {
		const movie = ref();
		const imdbLink = ref(null);
		const loading = ref(false);
		const error = ref(false);

		watchEffect(() => {
			document.body.scrollTop = 0;
			document.documentElement.scrollTop = 0;
		});

		const handleSearch = async () => {
			if (!props.imdbID) return;
			loading.value = true;
			error.value = false;
			try {
				let response = await axios.get(
					`https://www.omdbapi.com/?apikey=${process.env.VUE_APP_API_KEY}&i=${props.imdbID}`
				);
				loading.value = false;
				if (response.data.Error == "Incorrect IMDb ID.") {
					error.value = response.data.Error;

					swal({
						title: "Oops",
						text: "Incorrect movie ID",
						icon: "error",
						closeOnClickOutside: false,
					}).then((ok) => {
						if (ok) {
							router.push("/");
						}
					});
				} else {
					movie.value = response.data;
					imdbLink.value = `https://imdb.com/title/${movie.value.imdbID}`;
				}
			} catch (error) {
				loading.value = true;
				error.value = error.message ? error.message : error;
				swal("Oops!", "Something went wrong, please try again", "error");
				console.log({ error });
			}
		};

		handleSearch();

		return {
			movie,
			imdbLink,
			loading,
			error,
		};
	},
};
</script>

<style scoped>
.loader {
	position: absolute;
	top: 50%;
	left: 50%;
	transform: translate(-50%, -50%);
	color: #ed1c24;
}

main {
	padding-bottom: 80px;
}

.content {
	display: flex;
	justify-content: space-between;
}

.content .left,
.content .right {
	width: 46%;
}

.content .left {
	display: flex;
	flex-direction: column;
	gap: 15px;
}

.movie {
	width: 32%;
	margin-bottom: 55px;
	display: flex;
	flex-direction: column;
}

.movie img {
	width: 100%;
	height: 400px;
	margin-bottom: 0;
}

.movie .title {
	color: white;
	padding: 15px;
	margin: 0;
	text-align: center;
}

ul {
	list-style: none;
	border-radius: 8px;
}

ul span {
	color: #ed1c24;
}

ul li {
	padding: 10px;
	border-bottom: 1px dotted #444;
}

ul li:first-child {
	border-top: 1px dotted #444;
}

.btn-container {
	display: flex;
	gap: 30px;
}

button {
	outline: none;
	border: none;
	background-color: blue;
	padding: 12px;
	border-radius: 8px;
	color: white;
	font-size: 18px;
	cursor: pointer;
}

button:last-child {
	background-color: #ed1c24;
}

.right img {
	height: auto;
	width: auto;
}

@media screen and (max-width: 760px) {
	h1 {
		font-size: 20px;
	}
	h2 {
		font-size: 20px;
	}
	main {
		padding-bottom: 10rem;
	}
	.content {
		flex-direction: column-reverse;
		gap: 15px;
	}
	.content .left,
	.content .right {
		width: 100%;
	}

	.content .left {
		gap: 20px;
	}
	.content .right img {
		width: 100%;
		height: auto;
	}

	.btn-container {
		gap: 0px;
		justify-content: space-between;
	}
}
</style>
