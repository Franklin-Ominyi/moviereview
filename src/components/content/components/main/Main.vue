<template>
	<main class="content">
		<div class="input-container">
			<input
				type="text"
				placeholder="Search for movie"
				v-model="input"
				:class="{ active: isFilled }"
			/>
			<button :class="{ active: isFilled }" v-if="loading">
				<i class="fa fa-spinner fa-spin" aria-hidden="true"></i>
			</button>
			<button :class="{ active: isFilled }" @click="handleSearch" v-else>
				Search
			</button>
		</div>
		<div class="movies-wrapper" v-if="!error">
			<div class="movie" v-for="item in movies" :key="item.imdbID">
				<router-link :to="{ name: 'movie-details', params: { id: item.imdbID } }">
					<img :src="item.Poster" :alt="item.Title" />
					<div class="title">
						<span style="color: #ed1c24">Title:</span> {{ item.Title }}
					</div>
				</router-link>
			</div>
		</div>
	</main>
</template>

<script>
import { ref, watch, watchEffect } from "vue";
import axios from "axios";
import swal from "sweetalert";

export default {
	setup() {
		const input = ref("");
		const isFilled = ref(false);
		const movies = ref([]);
		const loading = ref(false);
		const error = ref(false);

		watchEffect(() => {
			document.body.scrollTop = 0;
			document.documentElement.scrollTop = 0;
		});

		watch(input, () => {
			if (input.value && input.value.length > 2) {
				isFilled.value = true;
			} else {
				isFilled.value = false;
			}
		});

		const handleSearch = async () => {
			if (!input.value || input.value.length <= 2) return;
			loading.value = true;
			error.value = false;
			try {
				let response = await axios.get(
					`https://www.omdbapi.com/?apikey=${process.env.VUE_APP_API_KEY}&s=${input.value}`
				);

				loading.value = false;
				if (response.data.Response == "True") {
					movies.value = response.data.Search;
				} else {
					movies.value = [];
					error.value = response.data.Error;
					swal("Oops!", error.value, "error");
				}
			} catch (error) {
				console.log({ error });
				swal("Oops!", "Something went wrong, please try again", "error");
				loading.value = false;
			}
		};

		return {
			input,
			isFilled,
			loading,
			handleSearch,
			movies,
			error,
		};
	},
};
</script>

<style scoped>
main {
	padding-bottom: 80px;
}
.input-container {
	display: flex;
	align-items: center;
	gap: 30px;
	width: 80%;
	margin: 0 auto;
}

input {
	border: none;
	outline: none;
	padding: 15px;
	border-radius: 8px;
	font-size: 18px;
	font-weight: 500;
	border: 2px solid #ddd;
	transition: border 0.5s ease;
	width: 60%;
}

input.active {
	border: 2px solid blue;
}

button {
	outline: none;
	border: none;
	border-radius: 8px;
	padding: 17px 25px;
	font-size: 18px;
	background-color: #ed1c24;
	color: white;
	font-weight: 400;
	cursor: pointer;
	transition: background-color 0.5s ease;
	cursor: not-allowed;
}

button.active {
	background-color: blue;
	cursor: pointer;
}

.movies-wrapper {
	margin-top: 50px;
	display: flex;
	flex-wrap: wrap;
	justify-content: space-between;
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
	background-color: black;
	color: white;
	padding: 15px;
	margin: 0;
	text-align: center;
}

@media screen and (max-width: 760px) {
	.input-container {
		gap: 10px;
		width: 100%;
	}

	input {
		font-size: 16px;
		font-weight: 400;
		border: 2px solid #ddd;

		width: 100%;
	}

	button {
		padding: 15px;
		font-size: 18px;
	}

	.movie {
		width: 100%;
	}
}
</style>
