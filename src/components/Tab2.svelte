<script lang="ts">
  import { actionSheetController } from "$ionic/svelte";

  import {
    photos as photosFromService,
    loadSaved,
    deletePicture,
    photoStore,
    addNewToGallery as addNewToGalleryFromService,
  } from "$services/photo";
  import { camera, trash, close } from "ionicons/icons";

  const photos = photosFromService;

  const showActionSheet = async (photo, position) => {
    const actionSheet = await actionSheetController.create({
      header: "Photos",
      buttons: [
        {
          text: "Delete",
          role: "destructive",
          icon: trash,
          handler: () => {
            deletePicture(photo, position);
          },
        },
        {
          text: "Cancel",
          icon: close,
          role: "cancel",
          handler: () => {
            // Nothing to do, action sheet is automatically closed
          },
        },
      ],
    });
    await actionSheet.present();
  };

  const addNewToGallery = () => {
    addNewToGalleryFromService();
  };

  // some trials and checks in code of pwa-camera elements. But there is too much going on there...
  const addNewToGallery2 = async () => {
    const camera = document.createElement("pwa-camera-modal");
    const stream = await navigator.mediaDevices.getUserMedia({
      video: true,
      audio: false,
      // ...constraints,
    });
    //@ts-ignore
    const imageCapture = new window.ImageCapture(stream.getVideoTracks()[0]);
    //@ts-ignore
    console.log("CAMERA", camera, window.ImageCapture, imageCapture);
    document.body.appendChild(camera);
    // await camera.componentOnReady();
  };

  loadSaved();
</script>

<ion-header>
  <ion-toolbar>
    <ion-title> Photo Gallery </ion-title>
  </ion-toolbar>
</ion-header>

<ion-content>
  <ion-header collapse="condense">
    <ion-toolbar>
      <ion-title size="large">Photo Gallery</ion-title>
    </ion-toolbar>
  </ion-header>

  <ion-grid>
    <ion-row>
      {#each $photoStore as photo, index}
        <ion-col size="6">
          <ion-img
            src={photo.webviewPath}
            on:click={() => {
              showActionSheet(photo, index);
            }}
          />
        </ion-col>
      {/each}
    </ion-row>
  </ion-grid>

  <ion-fab vertical="bottom" horizontal="center" slot="fixed">
    <ion-fab-button on:click={addNewToGallery}>
      <ion-icon icon={camera} />
    </ion-fab-button>
  </ion-fab>
</ion-content>

<style>
  ion-content ion-toolbar {
    --background: translucent;
  }
</style>
