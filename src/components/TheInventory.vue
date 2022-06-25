<template>
	<section class="inventory">
		<div class="inventory__wrapper">
			<aside class="inventory__sidebar">
				<img
					class="sidebar__image"
					src="/images/Img.png"
					alt="Sidebar img"
				/>
				<div class="inventory__skeleton sidebar__title"></div>
				<ul>
					<li
						class="inventory__skeleton sidebar__info"
						v-for="(info, i) in 6"
						:key="i"
					></li>
				</ul>
			</aside>
			<div class="content__wrapper">
				<ul
					class="inventory__content"
					@dragenter.prevent
					@dragover.prevent
					@drop="onDrop($event)"
				>
					<li
						@click="setActiveItem(item)"
						class="inventory__cell"
						:class="{
							inventory__cell_allowed: !item.empty,
						}"
						:style="{ backgroundImage: `url(/images/${item.icon})` }"
						v-for="(item, i) in items"
						:key="i"
						:draggable="!item.empty"
						@dragstart="startDrag(item)"
						:data-position="item.position"
					>
						<div v-if="!item.empty" class="inventory__quantity">
							{{ item.quantity }}
						</div>
					</li>
				</ul>
				<ModalWindow
					:activeItem="activeItem"
					:isModalOpened="isModalOpened"
					@closeModal="closeModal"
					@removeItem="removeItem"
				/>
			</div>
		</div>
		<footer class="inventory__footer">
			<div class="inventory__skeleton footer__title"></div>
			<svg
				class="footer__close-icon"
				width="12"
				height="12"
				viewBox="0 0 12 12"
				fill="none"
				xmlns="http://www.w3.org/2000/svg"
			>
				<path
					d="M12 1.05L10.95 0L6 4.95L1.05 0L0 1.05L4.95 6L0 10.95L1.05 12L6 7.05L10.95 12L12 10.95L7.05 6L12 1.05Z"
					fill="white"
				/>
			</svg>
		</footer>
	</section>
</template>

<script setup>
import ModalWindow from './ModalWindow.vue'
import { ref, reactive, onMounted, watch, computed } from 'vue'
import itemsArray from '../../public/data.json'

const items = ref([])
const inventoryMaxItems = 25
const draggingItem = ref(null)
const isModalOpened = ref(false)
const activeItem = ref(null)

onMounted(() => {
	const storage = JSON.parse(localStorage.getItem('items'))
	if (storage) {
		items.value = [...storage]
	} else {
		const inventoryArr = Array(inventoryMaxItems).fill({ empty: true })
		items.value = inventoryArr.map((item, index) => {
			const res = itemsArray.find(el => el.position === index)
			return res ? res : (item = { ...item, position: index })
		})

		localStorage.setItem('items', JSON.stringify(items.value))
	}
})

const setActiveItem = item => {
	if (item.empty) {
		return
	} else if (item === activeItem.value) {
		closeModal()
		return
	}
	activeItem.value = item
	isModalOpened.value = true
}

const closeModal = () => {
	isModalOpened.value = false
	activeItem.value = null
}

const removeItem = num => {
	if (activeItem.value.quantity - num <= 0) {
		items.value = items.value.map(i => {
			if (i === activeItem.value) {
				return (i = reactive({ empty: true, position: activeItem.value.position }))
			} else {
				return i
			}
		})
	} else {
		activeItem.value.quantity -= num
	}
	isModalOpened.value = false
	activeItem.value = null
}

const startDrag = item => (draggingItem.value = item)

const onDrop = event => {
	let itemPosition
	const index = event.composedPath().forEach(n => {
		const data = Object.assign({}, n.dataset)
		if (data.position) {
			itemPosition = +data.position
			return
		}
	})

	items.value[draggingItem.value.position] = items.value[itemPosition]
	items.value[itemPosition] = draggingItem.value

	items.value[draggingItem.value.position].position =
		draggingItem.value.position
	items.value[itemPosition].position = itemPosition

	draggingItem.value = null
}

watch(
	() => items,
	() => {
		localStorage.setItem('items', JSON.stringify(items.value))
	},
	{ deep: true }
)
</script>

<style lang="scss" scoped>
.inventory {
	padding: 32px;
	background-color: #1d1d1d;
	color: #fff;
	user-select: none;

	.inventory__wrapper {
		margin-bottom: 24px;
		display: flex;
	}

	.inventory__sidebar {
		padding: 18px 14px 24px;
		margin-right: 24px;
		display: flex;
		flex-direction: column;
		align-items: center;
		background: #262626;
		border: 1px solid #4d4d4d;
		border-radius: 12px;

		.sidebar__image {
			height: 240px;
			width: 100%;
			margin-bottom: 20px;
			display: block;
			border-radius: 8px;
		}

		.sidebar__title {
			min-width: 190px;
			height: 26px;
			margin-bottom: 26px;
			border-radius: 8px;
		}

		.sidebar__info {
			min-width: 170px;
			height: 10px;
			border-radius: 4px;

			&:not(:last-child) {
				margin-bottom: 16px;
			}
		}
	}

	.content__wrapper {
		position: relative;
		overflow: hidden;
		box-shadow: 0 0 0 1px#4d4d4d;
		border-radius: 12px;
	}

	.inventory__content {
		display: grid;
		grid-template-columns: repeat(5, 105px);
		grid-template-rows: repeat(5, 100px);
		gap: 1px;
		overflow: hidden;
		background: #262626;
		user-select: none;

		.inventory__cell {
			width: 100%;
			height: 100%;
			display: flex;
			align-items: center;
			justify-content: center;
			position: relative;
			box-shadow: 0 0 0 1px#4d4d4d;
			transition: all 0.1s ease-in-out;
			background-position: center;
			background-repeat: no-repeat;

			&_allowed {
				cursor: pointer;

				&:hover {
					background-color: #2f2f2f;
				}
			}
		}

		.inventory__quantity {
			width: 16px;
			height: 16px;
			padding: 2px 4px;
			color: #ffffff;
			position: absolute;
			bottom: 0;
			right: 0;
			text-align: center;
			background: #262626;
			border: 1px solid #4d4d4d;
			border-radius: 6px 0px 0px 0px;
			opacity: 0.4;
			font-size: 10px;
		}
	}

	.inventory__footer {
		padding: 18px;
		background: #262626;
		border: 1px solid #4d4d4d;
		border-radius: 12px;
		position: relative;

		.footer__title {
			max-width: 700px;
			width: 100%;
			height: 36px;
			background: linear-gradient(
				90deg,
				#3c3c3c 0%,
				#444444 51.04%,
				#333333 100%
			);
			border-radius: 12px;
		}

		.footer__close-icon {
			position: absolute;
			top: 14px;
			right: 14px;
			cursor: pointer;
		}
	}
}
</style>
