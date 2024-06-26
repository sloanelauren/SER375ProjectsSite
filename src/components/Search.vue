<template>
    <div class="search-container">
        <input type="text" v-model="searchTerm" placeholder="Search a project or an author..." class="searchbar"
            @keyup.enter="search()">
        <button type="button" @click="search()" class="search-button">
            <img src="@/assets/searchIcon.png" alt="">
        </button>
    </div>
    <div class="allButton">
        <button type="button" @click="showAllProjects" class="show-all-button">Show All Projects</button>
    </div>
    <div class="results-container" v-if="searchResults && searchResults.length > 0">
        <h1>Results</h1>
        <search-result v-for="searchResult in searchResults" :key="searchResult.display" :title="searchResult.Title"
            :author="searchResult.Author" :display="searchResult.display" />
    </div>
</template>
  
<script>
import SearchResult from "@/components/SearchResult.vue";
import { getProjects } from '../projects/projects-gatherer.js'

export default {
    name: 'SearchBar',
    data() {
        return {
            searchTerm: '',
            searchField: 'project',
            searchResults: null,
        };
    },
    created() {
        if (this.$route.query.query) {
            this.searchTerm = this.$route.query.query;
            this.search();
        }
    },
    methods: {
        search() {
            if (!this.searchTerm) {
                return;
            }
            const query = this.searchTerm.toLowerCase();
            const matches = [];
            const cache = new Set();
            const projectData = getProjects(); // Fetch all projects
            projectData.projects.forEach(project => {
                const heading = project.header;
                if (heading.title.toLowerCase().includes(query) || heading.author.toLowerCase().includes(query)) {
                    const display = `${heading.title}-${heading.author}`;
                    if (!cache.has(display)) {
                        matches.push({ Title: heading.title, Author: heading.author, display });
                        cache.add(display);
                    }
                }
            });
            matches.sort((a, b) => (a.display > b.display) ? 1 : -1);
            this.searchResults = matches;
        },
        showAllProjects() {
            const matches = [];
            const cache = new Set();
            const projectData = getProjects(); // Fetch all projects
            projectData.projects.forEach(project => {
                const heading = project.header;
                if (heading.title.toLowerCase() || heading.author.toLowerCase()) {
                    const display = `${heading.title}-${heading.author}`;
                    if (!cache.has(display)) {
                        matches.push({ Title: heading.title, Author: heading.author, display });
                        cache.add(display);
                    }
                }
            });
            matches.sort((a, b) => (a.display > b.display) ? 1 : -1);
            this.searchResults = matches;
        },
    },
    components: {
        SearchResult,
    },
};
</script>
  
<style scoped>
.search-container {
    display: flex;
    align-items: center;
    justify-content: center;
    margin-top: 2vh;
}

.searchbar {
    width: 40vw;
    height: 30px;
    margin-right: 10px;
    padding: 20px;
    font-family: "Poppins";
    border-radius: 20px;
    outline: none;
    border: none;
}

.search-button {
    border: none;
    outline: none;
    background-color: transparent;
    cursor: pointer;
    width: 3vw;
    min-width: 30px;
    height: 0;
    padding-top: 10%;
    position: relative;
}

.results-container {
    background-color: rgb(32, 33, 38);
    padding-bottom: 20vh;
}

.allButton {
    display: flex;
    justify-content: center;
    /* Center horizontally */
    align-items: center;
    /* Center vertically */
    margin-top: 2vh;
    /* Adjust margin-top as needed */
}

.allButton>button {
    text-decoration: none;
    color: white;
    font-family: "Poppins";
    background-color: #7473BF;
    padding: .7rem;
    border: none;
    border-radius: 1rem;
    margin-bottom: 5vh;
    font-weight: 500;
}

.allButton>button:hover {
    background-color: #5d5b99;
    transition: background-color 0.3s ease-in-out;
}

button>img {
    width: 100%;
    height: 100%;
    object-fit: contain;
    position: absolute;
    top: 0;
    left: 0;
    transition: filter 0.3s, color 0.3s;
}

button>img:hover {
    filter: brightness(0) invert(1);
}

h1 {
    margin-bottom: 2vh;
    margin-top: 2vh;
    font-size: x-large;
}
</style>
