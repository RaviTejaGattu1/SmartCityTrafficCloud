<template>
	<div class="right-panel">
		<div v-if="showCctvPanel" class="cctv-panel">
			<div class="toggle">
				<label class="switch">
					<input type="checkbox" id="traffic" v-model="traffic">
					<span class="slider round"></span>
				</label>
				<span class="label-text">Traffic</span>
			</div>
			<div class="toggle">
				<label class="switch">
					<input type="checkbox" id="cctv" v-model="cctv">
					<span class="slider round"></span>
				</label>
				<span class="label-text">CCTVs</span>
			</div>
			<div class="toggle">
				<label class="switch">
					<input type="checkbox" id="drones" v-model="drones">
					<span class="slider round"></span>
				</label>
				<span class="label-text">IOT</span>
			</div>
		</div>
		<div v-if="showAddCctvPanel" class="add-cctv-panel">
			<div>
				Click on the map to choose a point to add new cameras.
				<br><br>
			</div>
			<div class="d-flex column">
				<div>
    <div>
        Latitude
    </div>
    <input type="text" class="textbox add-cctv" v-model="addCctv.lat">
</div>
<div>
    <div>
        Longitude
    </div>
    <input type="text" class="textbox add-cctv" v-model="addCctv.lng">
</div>
<div>
    <button class="button" @click="addCameras()">Add Camera</button>
</div>
			</div>
		</div>
		<div v-if="showDronePanel" class="drone-panel">
            <div>Manage CCTVs</div>
            <div v-if="cameraLocations.length > 0">
                <table>
                    <thead>
                        <tr>
                            <th>Camera ID</th>
                            <th>Latitude</th>
                            <th>Longitude</th>
                            <th>Action</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="(data, index) in cameraLocations" :key="index">
                            <td>{{ data.id }}</td>
                            <td>{{ data.coords.lat }}</td>
                            <td>{{ data.coords.lng }}</td>
                            <td><button @click="deleteCamera(data)"><i class="fa fa-trash"></i></button></td>
                        </tr>
                    </tbody>
                </table>
            </div>
            <div v-else>
                No cameras available.
            </div>
        </div>
    </div>
</template>

<script>
import api from "@/services/api.service.js";
export default {
	props: ['activeTab', 'coords'],
	data() {
		return {
			traffic: false,
			cctv: false,
			drones: false,
			showCctvPanel: false,
			showAddCctvPanel: false,
			showDronePanel: false,
			addCctv: {
				lat: "",
				lng: ""
			},
			cameraLocations: ""
		}
	},
	mounted() {
		this.currentComponent();
		this.getMarkers();
	},
	methods: {
		currentComponent() {
			if (this.activeTab === 'traffic') {
				this.showCctvPanel = true;
				this.showAddCctvPanel = false;
				this.showDronePanel = false;
			} else if (this.activeTab === 'cctv') {
				this.showCctvPanel = false;
				this.showAddCctvPanel = true;
				this.showDronePanel = false;

			} else if (this.activeTab === 'drones') {
				this.showDronePanel = true;
				this.showCctvPanel = false;
				this.showAddCctvPanel = false;
			}
		},

		getMarkers() {
			api({
				url: `/cameras`,
				method: "get"
			})
				.then(response => {
					const responseData = response.data;
					console.log(responseData);
					this.cameraLocations = responseData
				})
				.catch(e => console.log(e));
		},

		addCameras() {
			const coords = {
				lat: this.addCctv.lat,
				lng: this.addCctv.lng
				
			}
			api({
				url: `/cameras`,
				method: "post",
				data: {
					"coords": coords,
				}
			})
			.then(response => {
        // Displaying the alert after the server has successfully added the camera
        window.alert('The camera has been added.');
        console.log(response.data);
        this.getMarkers();  // Refreshing the camera markers on the map
    })
    .catch(e => {
        console.error(e);
        window.alert('Failed to add the camera.'); // Providing feedback on failure
    });

		},

		deleteCamera(data) {
			api({
				url: `/camera/${data.camera_id}`,
				method: "delete",
			})
				.then(response => {
					const responseData = response;
					console.log(responseData);
					this.getMarkers()
				})
				.catch(e => console.log(e));
		}
	},
	watch: {
		traffic(newVal) {
			this.$emit('toggle-traffic', newVal);
			// console.log(newVal)
		},
		cctv(newVal) {
			this.$emit('toggle-cctv', newVal);
		},
		drones(newVal) {
			this.$emit('toggle-drones', newVal);
		},
		activeTab(newVal) {
			this.currentComponent()
			this.getMarkers();
		},
		coords(newVal) {
			this.addCctv = newVal;
		}
	}
}
</script>

<style scoped>

.button {
    background-color: #4CAF50; /* Green */
    border: none;
    color: white;
    padding: 15px 32px;
    text-align: center;
    text-decoration: none;
    display: inline-block;
    font-size: 16px;
    margin: 4px 2px;
    cursor: pointer;
    border-radius: 4px;
}

.textbox {
    width: 100%;
    padding: 12px 20px;
    margin: 8px 0;
    display: inline-block;
    border: 1px solid #ccc;
    border-radius: 4px;
    box-sizing: border-box;
}

.right-panel {
	display: flex;
	flex-direction: column;
	padding: 20px;
	border-left: 1px solid #ddd;
	background-color: #f8f9fa;
	/* height: 100vh; */
	width: 15%;
}

.toggle {
	margin-bottom: 10px;
}

.label-text {
	margin-left: 10px;
	font-size: 16px;
	color: #333;
}

.switch {
	position: relative;
	display: inline-block;
	width: 60px;
	height: 34px;
}

.switch input {
	opacity: 0;
	width: 0;
	height: 0;
}

.slider {
	position: absolute;
	cursor: pointer;
	top: 0;
	left: 0;
	right: 0;
	bottom: 0;
	background-color: #ccc;
	transition: .4s;
	border-radius: 34px;
}

.slider:before {
	position: absolute;
	content: "";
	height: 26px;
	width: 26px;
	left: 4px;
	bottom: 4px;
	background-color: white;
	transition: .4s;
	border-radius: 50%;
}

input:checked+.slider {
	background-color: #2196F3;
}

input:focus+.slider {
	box-shadow: 0 0 1px #2196F3;
}

input:checked+.slider:before {
	transform: translateX(26px);
}

table {
    width: 100%;
    border-collapse: collapse;
    margin-top: 10px;
}

th, td {
    border: 1px solid #ddd;
    padding: 8px;
    text-align: left;
}

th {
    background-color: #4CAF50;
    color: white;
}

</style>