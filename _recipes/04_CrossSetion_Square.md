---
layout: recipe
title:  "CrossSection Square"
image:  default.png
script: >
  use("Cindy3D");
  use("CindyXR");
  initxrcindy3d(referencemode->"local-floor");

  vertices =
    [
      [1,0,0,1],
      [0,0,0,1],
      [0,1,0,1],
      [1,1,0,1]
    ];
  lineSlope = vertices_2-vertices_1;
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

  draw3Dline(point, slope) :=(
      //this should eventually be indefinate, instead of 100m
      draw3d(point+1000*slope,point-1000*slope,size->.1, color->[.7,.7,.7]);
  );

  draw3Dquad(vertices);
    //draw vertices
    repeat(length(vertices),i,draw3d(vertexList_i, size->.3, color->[1,0,0]));

      //draw line segments ab, bc, cd, ad
      draw3d(vertexList_1,vertexList_2, color->[0,0,0]);
      draw3d(vertexList_2,vertexList_3, color->[0,0,0]);
      draw3d(vertexList_3,vertexList_4, color->[0,0,0]);
  draw3d(vertexList_1,vertexList_4, color->[0,0,0]);

      //draw triangle abc
      fillpoly3d(vertices, color->[.9,.9,.9]);
  );

  //begin applet
  begin3d();

  //environment parameters
      size3d(.1);

  //set bg color
      background3d([0.9,0.9,0.9]);

  //draw triangle abc
  draw3Dquad(vertices);

  //TODO draw cross-section

  //draw tracked controllers
      draw3Dxrinputsources(getxrinputsources());

  end3d();
  </script>

  <script id="xrselecthold" type="text/x-cindyscript">
  projectOntoLine(position,point,slope):=(
      slopeHat = slope/|slope|;
      ((position-point)*slopeHat)*slopeHat + point;
  );

  //drag the verticies when proximal and holding the squeeze button, but keep it on its line.
  tolerance = .5;
  controllerPosition = inputsource.gripSpaceTransform.position;
  repeat(length(vertices), i,
      print(text(i) + ": " + text(|controllerPosition - vertices_i| < tolerance));
      if(|controllerPosition - vertices_i| < tolerance,
          vertices_i = projectOntoLine(controllerPosition, vertices_i, lineSlope);
      );
  );
  </script>

  <script id="xrsqueezehold" type="text/x-cindyscript">
  //drag the verticies when proximal and holding the squeeze button
  tolerance = .5;
  controllerPosition = inputsource.gripSpaceTransform.position;
  repeat(length(vertices), i,
      print(text(i) + ": " + text(|controllerPosition - vertices_i| < tolerance));
      if(|controllerPosition - vertices_i| < tolerance,
          vertices_i = controllerPosition;
          lineSlope = vertices_2-vertices_1;
      );
  );
  </script>
---
