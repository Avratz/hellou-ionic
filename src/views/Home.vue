<template>
	<ion-page>
		<ion-header>
			<ion-toolbar>
				<ion-title>Blank</ion-title>
			</ion-toolbar>
		</ion-header>

		<ion-content>
			<div id="container">
				<p>{{ locationData }}</p>
				<p>{{ notificationsPending }}</p>
				<p>{{ notifs }}</p>

				<ion-button size="small" @click="getLocation()"
					>get location</ion-button
				>
				<ion-button size="small" @click="scheduleNotif()"
					>schedule notification</ion-button
				>
				<ion-button size="small" @click="startGeofence()"
					>geofence?</ion-button
				>
			</div>
		</ion-content>
	</ion-page>
</template>

<script>
import {
	IonContent,
	IonHeader,
	IonPage,
	IonTitle,
	IonToolbar,
	IonButton,
} from '@ionic/vue'
import { ref } from 'vue'
import { Plugins } from '@capacitor/core'
import { Geofence } from '@ionic-native/geofence';

export default {
	name: 'Home',
	components: {
		IonContent,
		IonButton,
		IonHeader,
		IonPage,
		IonTitle,
		IonToolbar,
	},
	setup() {
		const locationData = ref({})
		const notificationsPending = ref({})
		const notifs = ref({})
		
		const startGeofence =  () => {
			Geofence.initialize().then(
			() => console.log('Geofence Plugin Ready'),
			(err) => console.log(err)
  )
			console.warn(Geofence)
		}

		const getLocation = async () => {
			const { Geolocation } = Plugins
			const results = await Geolocation.getCurrentPosition()
			locationData.value = {
				lat: results.coords.latitude,
				long: results.coords.longitude,
			}
		}

		const scheduleNotif = async () => {
			const { LocalNotifications } = Plugins
			notifs.value = await LocalNotifications.schedule({
				notifications: [
					{
						title: 'Title',
						body: 'Body',
						id: 1,
						schedule: { at: new Date(Date.now() + 1000 * 5) },
						sound: null,
						attachments: null,
						actionTypeId: '',
						extra: null,
					},
				],
			})
			const pending = await LocalNotifications.getPending()
			console.warn(pending)
			notificationsPending.value = pending
			console.warn(notificationsPending)
		}

		return {
			locationData,
			getLocation,
			scheduleNotif,
			notificationsPending,
			notifs,
			startGeofence
		}
	},
	mounted() {},
}
</script>

<style scoped>
#container {
	text-align: center;

	position: absolute;
	left: 0;
	right: 0;
	top: 50%;
	transform: translateY(-50%);
}

#container strong {
	font-size: 20px;
	line-height: 26px;
}

#container p {
	font-size: 16px;
	line-height: 22px;

	color: #8c8c8c;

	margin: 0;
}

#container a {
	text-decoration: none;
}
</style>
