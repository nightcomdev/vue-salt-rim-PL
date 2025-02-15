<template>
    <RouterLink class="cocktail-grid-item" :to="{ name: 'cocktails.show', params: { id: cocktail.slug } }">
        <div class="cocktail-grid-item__graphic">
            <div class="cocktail-grid-item__badges">
                <div v-if="cocktail.isFavorited" class="cocktail-badge">
                    <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24">
                        <path fill="none" d="M0 0H24V24H0z" />
                        <path d="M20.243 4.757c2.262 2.268 2.34 5.88.236 8.236l-8.48 8.492-8.478-8.492c-2.104-2.356-2.025-5.974.236-8.236C5.515 3 8.093 2.56 10.261 3.44L6.343 7.358l1.414 1.415L12 4.53l-.013-.014.014.013c2.349-2.109 5.979-2.039 8.242.228z" />
                    </svg>
                </div>
            </div>
            <img class="cocktail-grid-item__graphic__image" :data-img-src="mainCocktailImageUrl" :src="placeholderImage" alt="Main image of the cocktail">
        </div>
        <div class="block-container cocktail-grid-item__content">
            <h2 class="cocktail-grid-item__title">{{ cocktail.name }}</h2>
            <div class="cocktail-grid-item__rating">
                <CocktailRating :user-rating="cocktail.rating.user" :average-rating="cocktail.rating.average" />
            </div>
            <p v-if="shortIngredients.length > 0" class="cocktail-grid-item__ingredients">{{ shortIngredients.join(', ') }}</p>
            <ul class="cocktail-tags">
                <li v-for="tag in cocktail.tags.slice(0, maxTags)" :key="tag.id" class="chip">{{ tag.name }}</li>
                <li v-if="cocktail.tags.length - maxTags > 0" class="chip" style="opacity: 0.6;">+ {{ cocktail.tags.length - maxTags }} more</li>
            </ul>
        </div>
    </RouterLink>
</template>

<script>
import ApiRequests from '@/ApiRequests.js'
import { thumbHashToDataURL } from 'thumbhash'
import CocktailRating from './CocktailRating.vue'

export default {
    components: {
        CocktailRating
    },
    props: {
        cocktail: {
            type: Object,
            default() {
                return {}
            }
        },
        observer: {
            type: Object,
            default() {
                return {}
            }
        }
    },
    data() {
        return {
            maxTags: 4
        }
    },
    computed: {
        mainImage() {
            const images = this.cocktail.images

            return images.sort((a, b) => a.sort - b.sort)[0]
        },
        placeholderImage() {
            if (this.cocktail.image_hash) {
                return thumbHashToDataURL(
                    Uint8Array.from(atob(this.cocktail.image_hash), c => c.charCodeAt(0))
                )
            }

            if (this.cocktail.images && this.cocktail.images.length > 0) {
                return thumbHashToDataURL(
                    Uint8Array.from(atob(this.mainImage.placeholder_hash), c => c.charCodeAt(0))
                )
            }

            return ''
        },
        mainCocktailImageUrl() {
            if (this.cocktail.image_url) {
                return ApiRequests.imageThumbUrl(this.cocktail.main_image_id)
            }

            if (this.cocktail.images && this.cocktail.images.length > 0) {
                return ApiRequests.imageThumbUrl(this.mainImage.id)
            }

            return '/no-cocktail.jpg'
        },
        shortIngredients() {
            return this.cocktail.ingredients.map(i => i.ingredient.name)
        }
    },
    mounted() {
        this.observer.observer.observe(this.$el)
    }
}
</script>
<style scoped>
.cocktail-grid-item {
    --_image-size: 325px;
    --_clr-content: var(--clr-gray-500);
    text-align: center;
    border-width: 3px;
    cursor: pointer;
    text-decoration: none;
    display: flex;
    flex-direction: column;
}

.cocktail-grid-item:hover .block-container {
    border-bottom-color: var(--clr-accent-500);
}

.dark-theme .cocktail-grid-item {
    --_clr-content: var(--clr-gray-400);
}

.cocktail-grid-item__title {
    font-size: 1.7rem;
    font-family: var(--font-heading);
    font-weight: var(--fw-bold);
    line-height: 1.3;
}

.cocktail-grid-item__content {
    padding: 1.5rem;
    border-radius: var(--radius-3);
    margin-top: -2.25rem;
    height: 100%;
    display: flex;
    flex-direction: column;
    gap: var(--gap-size-2);
}

.cocktail-grid-item:hover .cocktail-grid-item__title {
    color: var(--clr-link-color-hover);
}

.cocktail-grid-item__ingredients {
    color: var(--_clr-content);
    font-size: 0.85rem;
}

.cocktail-grid-item__graphic {
    isolation: isolate;
}

.cocktail-grid-item__graphic img {
    width: 100%;
    height: var(--_image-size);
    border-top-left-radius: var(--radius-3);
    border-top-right-radius: var(--radius-3);
    object-fit: cover;
}

.cocktail-tags {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    list-style: none;
    padding: 0;
    gap: var(--gap-size-1);
}

.cocktail-grid-item__badges {
    position: absolute;
    top: 0.5rem;
    left: 0.5rem;
    z-index: 1;
}

.cocktail-badge {
    background-color: rgba(0, 0, 0, .5);
    display: flex;
    align-items: center;
    justify-content: center;
    width: 22px;
    height: 22px;
    border-radius: var(--radius-round);
}

.cocktail-badge svg {
    fill: rgba(255, 255, 255, .9);
    width: 12px;
    height: 12px;
}

@media (max-width: 545px) {
    .cocktail-grid-item {
        --_image-size: 200px;
    }

    .cocktail-grid-item__title {
        font-size: 1.25rem;
    }

    .cocktail-grid-item__content {
        padding: var(--gap-size-3);
        border-radius: var(--radius-2);
        gap: var(--gap-size-1);
    }

    .cocktail-grid-item__graphic img {
        border-top-left-radius: var(--radius-2);
        border-top-right-radius: var(--radius-2);
    }

    .cocktail-tags .chip {
        padding: 1px 5px;
        font-size: 0.65rem;
    }

    .cocktail-grid-item__ingredients {
        font-size: 0.75rem;
    }
}
</style>
