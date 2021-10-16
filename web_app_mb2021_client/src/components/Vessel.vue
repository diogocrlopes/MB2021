<template>
  <l-layer-group>
    <l-polygon :lat-lngs="vesselPolygon" :color="boatColor" />
    <l-polygon :lat-lngs="rudderPolygon" :color="rudderColor" />
  </l-layer-group>
</template>

<script>
import {LPolygon, LLayerGroup} from "vue2-leaflet";
import GeographicLib from "geographiclib";;

export default {
  name: "Vessel",
  components: {
    LPolygon,
    LLayerGroup
  },
  props: [
      "vesselCoord",
  ],
  data:() => {
    return {
      length: 13,
      beam: 3.5,
      boatColor: "#0074B7",
      rudderColor: "#ff0000",
      ship: [
        [-0.430382, -0.395089],
        [-0.363991, -0.462863],
        [-0.296946, -0.5],
        [0.309095, -0.5],
        [0.376066, -0.462803],
        [0.440852, -0.355334],
        [0.494233, -0.119366],
        [0.5, 0.0],
        [0.494233, 0.119366],
        [0.440852, 0.355334],
        [0.376066, 0.462803],
        [0.309095, 0.5],
        [-0.296946, 0.5],
        [-0.363991, 0.462863],
        [-0.430382, 0.395089],
        [-0.5, 0.25955],
        [-0.5, -0.25955],
        [-0.430382, -0.395089],
      ],
      vesselPolygon: "",
      rudderPolygon: "",
      rudderAngle: 0, //Here need to change in API
    }
  },
  methods: {
    createGeoCoords: function(listOfPoints, translade = 0) {
      var vesselCoords = listOfPoints;
      var scaledCoords = vesselCoords.map((v) => [
        v[0] * this.length + translade,
        v[1] * this.beam,
      ]);
      var angleRad = this.toRadians(this.vesselCoord[2]);
      var rotatedCoords = scaledCoords.map((v) => {
        var x = v[0] * Math.cos(angleRad) - v[1] * Math.sin(angleRad);
        var y = v[0] * Math.sin(angleRad) + v[1] * Math.cos(angleRad);

        return [x, y];
      });
      var geoCoords = rotatedCoords.map((v) =>
          this.latLngFromDistance(
              this.vesselCoord[0],
              this.vesselCoord[1],
              v[0],
              v[1]
          )
      );
      return geoCoords;
    },
    createRudder() {
      var p1 = [-0.5 * this.length, 0.0];
      var p2 = [
        p1[0] - 2 * Math.cos(this.rudderAngle),
        p1[1] - 2 * Math.sin(this.rudderAngle),
      ];
      var rudderCoords = [p1, p2];
      var angleRad = this.toRadians(this.vesselCoord[2]);
      var rotatedCoords = rudderCoords.map((v) => {
        var x = v[0] * Math.cos(angleRad) - v[1] * Math.sin(angleRad);
        var y = v[0] * Math.sin(angleRad) + v[1] * Math.cos(angleRad);

        return [x, y];
      });
      var geoCoords = rotatedCoords.map((v) =>
          this.latLngFromDistance(
              this.vesselCoord[0],
              this.vesselCoord[1],
              v[0],
              v[1]
          )
      );
      return geoCoords;
    },
    toRadians: function(angleDeg) {
      return angleDeg * (Math.PI / 180);
    },
    latLngFromDistance: function(
        baseLatitude,
        baseLongitude,
        distanceInMetersX,
        distanceInMetersY
    ) {
      var azi = this.toDegrees(
          Math.atan2(distanceInMetersX, distanceInMetersY)
      );
      var distance = Math.sqrt(
          Math.pow(distanceInMetersX, 2) + Math.pow(distanceInMetersY, 2)
      );

      return this.latLngFromAngleLength(
          baseLatitude,
          baseLongitude,
          azi,
          distance
      );
    },
    toDegrees: function(angleRad) {
      return (angleRad * 180) / Math.PI;
    },
    latLngFromAngleLength: function(
        baseLatitude,
        baseLongitude,
        angleDeg,
        length
    ) {
      var geod = GeographicLib.Geodesic.WGS84;
      var azi = angleDeg;
      var distance = length;

      var r = geod.Direct(baseLatitude, baseLongitude, azi, distance);

      return [r.lat2, r.lon2];
    },
  },
  mounted() {
    this.vesselPolygon = this.createGeoCoords(this.ship);
    this.rudderPolygon = this.createRudder();
  },
}
</script>

<style scoped>

</style>