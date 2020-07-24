---
layout: recipe
title:  "CrossSection Cube"
image:  default.png
script: >
  use("Cindy3D");
  use("CindyXR");
  initxrcindy3d(referencemode->"local-floor");

  vertices =
    [
      [0,0,0,1],
      [1,0,0,1],
      [1,1,0,1],
      [0,1,0,1],
      [0,0,1,1],
      [0,1,1,1],
      [1,0,1,1],
      [1,1,1,1]
    ];
  //TODO define cross-section plane

  </script>

  <script id="xrdraw" type="text/x-cindyscript">
  //Function Definitions:
  draw3Dxrinputsources(xrinputsources) :=(
     repeat(length(xrinputsources), i,
         inputsource = xrinputsources_i;
         // We only want input sources regarding tracked controllers.
         if (inputsource.targetRayMode == "tracked-pointer",
             // Draw a ball at the position of the controller and color it depending on whether button 1 is pressed.
             isbuttonpressed = inputsource.gamepad.buttons_1.pressed;
             color = if(isbuttonpressed, hue(0.1), hue(0.5));
             color3d(color);
             size3d(.3);
             draw3d(inputsource.gripSpaceTransform.position);
         );
     );
  );

  draw3Dtriangle(vertexList) :=(
         //draw points abc
   draw3d(vertexList_1, size->.3, color->[1,0,0]);
   draw3d(vertexList_2, size->.3, color->[0,1,0]);
   draw3d(vertexList_3, size->.3, color->[0,0,1]);

     //draw line segments ab, bc, ac
   draw3d(vertexList_1,vertexList_2, color->[0,0,0]);
   draw3d(vertexList_2,vertexList_3, color->[0,0,0]);
   draw3d(vertexList_1,vertexList_3, color->[0,0,0]);

     //draw triangle abc
   fillpoly3d([vertices_1,vertices_2,vertices_3], color->[.9,.9,.9]);
  );

  //begin applet
  begin3d();

  //environment parameters
     size3d(.1);

  //set bg color
  background3d([0.9,0.9,0.9]);

  //draw faces vertices = [a,b,c,d,e]
  faces =
  [
   [vertices_1,vertices_2,vertices_3,vertices_4], //quad abcd
   [vertices_1,vertices_2,vertices_5], //quad abge
   [vertices_2,vertices_3,vertices_5], //quad adfe
   [vertices_3,vertices_4,vertices_5], //quad hgef
   [vertices_4,vertices_1,vertices_5] //quad hcdf

  ];

  repeat(length(faces),i, draw3Dtriangle(faces_i));

  //draw vertices
  repeat(length(vertices),i,draw3d(vertexList_i, size->.3, color->[1,0,0]));


  //draw tracked controllers
   draw3Dxrinputsources(getxrinputsources());

  end3d();
---
