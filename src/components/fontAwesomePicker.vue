<template>
	<div id="iconPicker" v-click-outside="outside">
		<div class="iconPicker__header">
			<input type="text" :placeholder="searchPlaceholder" @click="openPicker" @focus="openPicker" @keyup="filterIcons($event)">
		</div>
		<div v-if="showPicker" class="iconPicker__body">
			<div class="iconPicker__icons">
				<a
					href="#"
					@click.stop.prevent="getIcon(icon.value, icon.name)"
					:class="`item ${selected === icon.name ? 'selected' : ''}`"
					v-for="icon in icons"
					:key="icon.value"
				>
					<i :class="'fas fa-'+icon.name"></i>
				</a>
			</div>
		</div>
	</div>
</template>

<script>
import icons from './icons';

export default {
	name: 'fontAwesomePicker',
	props: ['seachbox'],
	data () {
		return {
			selected: '',
			icons,
			searchVal: '',
			showPicker: false,
		};
	},
	computed: {
		searchPlaceholder () {
			return this.seachbox || 'search box';
		},
	},
	methods: {
		outside () {
			if (this.showPicker !== false) this.showPicker = false;
		},
		getIcon (icon, key) {
			this.selected = key;
			// NEED TO FIX CONVERT METHOD
			// this.convert(icon);
			this.selectIcon(icon.toUpperCase());
		},
		convert (value) {
			const newValue = value
				.charCodeAt(1)
				.toString(10)
				.replace(/\D/g, '');

			let hexValue = Number(newValue).toString(16);

			while (hexValue.length < 4) {
				hexValue = `0${hexValue}`;
			}
		},
		selectIcon (value) {
			const result = {
				className: this.selected,
				cssValue: value,
			};
			this.$emit('selectIcon', result);
		},
		filterIcons (event) {
			const search = event.target.value.trim();
			this.searchVal = search;
			let filter = [];

			if (search.length > 3) {
				filter = icons.filter((item) => {
					const regex = new RegExp(search, 'gi');
					return item.name.match(regex);
				});
			} else if (search.length === 0) {
				this.icons = icons;
			}

			if (filter.length > 0) {
				this.icons = filter;
			}
		},
		openPicker () {
			console.log('openPicker', this.showPicker);
			this.showPicker = true;
		},
	},
	directives: {
		'click-outside': {
			bind: function (el, binding, vNode) {
				// Provided expression must evaluate to a function.
				if (typeof binding.value !== 'function') {
					const compName = vNode.context.name;
					let warn = `[Vue-click-outside:] provided expression '${binding.expression}' is not a function, but has to be`;
					if (compName) { warn += `Found in component '${compName}'`; }
					console.warn(warn);
				}
				// Define Handler and cache it on the element
				const bubble = binding.modifiers.bubble;
				const handler = (e) => {
					if (bubble || (!el.contains(e.target) && el !== e.target)) {
						binding.value(e);
					}
				};
				el.__vueClickOutside__ = handler;

				// add Event Listeners
				document.addEventListener('click', handler);
			},

			unbind: function (el, binding) {
				// Remove Event Listeners
				document.removeEventListener('click', el.__vueClickOutside__);
				el.__vueClickOutside__ = null;
			},
		},
	},
};
</script>

<style>
	#iconPicker {
		position: relative;
		max-width: 292px;
		/* background: #f3f3f3; */
	}
	.iconPicker__header {
		/* padding: 1em; */
	}
	.iconPicker__header input {
		/* width: 100%;
		padding: 1em;
		background-color: white !important;
		color: black !important; */
	}
	.iconPicker__body {
		position: absolute;
		z-index: 999;
		background-color: #1B222D;
		color: white;
		max-height: 250px;
		overflow: auto;
		padding: 1em 0 1em 1em;
		border-radius: 0 0 8px 8px;
		border: 1px solid #ccc;
	}
	.iconPicker__icons {
		display: flex;
		flex-direction: row;
		max-width: inherit;
		flex-wrap: wrap;
		flex-grow: 2;
		justify-content: center;
	}
	.iconPicker__icons .item {
		flex: 1 0 30%;
	    width: 40px;
	    height: 40px;
	    padding: 7px 1em;
	    text-align: center;
	    border-radius: 3px;
	    font-size: 14px;
	    color: inherit;
	}
	.iconPicker__icons .item.selected {
		box-sizing: border-box;
		border: 2px solid #F78828 !important;
		outline-offset: -2px;
		border-radius: 5px !important;
	}
	.iconPicker__icons .item i {
		box-sizing: content-box;
	}
</style>
