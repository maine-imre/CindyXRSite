---
layout: recipe
title:  "CrossSection Square"
image:  default.png
script: >
  <script id="init" type="text/x-cindyscript">
          use("Cindy3D");
          use("CindyXR");
          initxrcindy3d(referencemode->"local-floor");
      //setup controller
    xrControllers =  getxrinputsources();

      size3d(.5); //default size.

    circlePar(w, basis0, basis1, center, radius) := (radius*(sin(w)*basis1+cos(w)*basis0) + center);  //parameterized fn for circle

      drawCircle(center, radius, basis0, basis1) := (
        repeat(n,i,
          w1 = i/n*360;
          w2 = (i+1)/n*360;
          draw3d(radius*circlePar(w1, basis0, basis1, center, radius),radius*circlePar(w2, basis0, basis1, center, radius)); //draw circle as a set of line segments
        );
      );

      crossSectSphere(sCenter, sRadius, pPos, pNorm) := (
          pNorm = pNorm / |pNorm|;
        center = (pPos-sCenter)*pNorm + sCenter;
          radius = re(sqrt(sRadius^2+|center-sCenter|^2));

          //need to define the basis with crossproduct
          basis0 = cross(pNorm,[1,0,0]);
          if(|basis0| == 0)
            basis0 = cross(pNorm,[0,1,0]);
        else
          basis0 = basis0/|basis0|;
        basis1 = cross(pNorm,basis0);

        drawCircle(center, radius, basis0, basis1);
      );

      //Sphere Crossection Parameters
        sphereCenter = [0,0,0];
        sphereRadius = 1;

      xrrenderfunction() := (
      begin3d();

      //setup controller - 1 indexed
        xrControllers =  getxrinputsources();
          //grab the first available controllers
          inputsourceL = xrControllers_1;
          inputsourceR = xrControllers_2;

          // in a new plugin design, these functions should be moved outside the render function.
          // #1 Draw Controller Position, color appropriate for buttons.
          if (inputsourceL.targetRayMode == "tracked-pointer",
              // Draw a ball at the position of the controller and color it depending on whether button 1 is pressed.
              //buttons_1 is "squeeze button" for vive and quest.
              //buttons_3 is "select button" for vive, but not quest.
              isSelectedL = inputsourceL.gamepad.buttons_3.pressed;
              isSqueezedL = inputsourceL.gamepad.buttons_1.pressed;
              controllerColor = [0,0,0];
              if(isSelectedL, controllerColor = controllerColor + [1,0,0]);
              if(isSqueezedL, controllerColor = controllerColor + [0,1,0]);
              //reads the position in homogeneous coordinates
              //homogeneous coordinates end in 1 if tracked, zero if not tracked
              draw3d(inputsourceL.gripSpaceTransform.position, color->controllerColor, size->.5);

              //use this controller to move the vertex 'c' if it is proximal to to the line.
              //TODO drag point c instead of reassigning position.
              positionL  = inputsourceL.gripSpaceTransform.position;
          );

          // Use the 2nd controller to draw an intersecting segment
          // #2 Draw Controller Position, color appropriate for buttons.
          if (inputsourceR.targetRayMode == "tracked-pointer",
              // Draw a ball at the position of the controller and color it depending on whether button 1 is pressed.
              //buttons_1 is "squeeze button" for vive and quest.
              //buttons_3 is "select button" for vive, but not quest.
              isSelectedR = inputsourceR.gamepad.buttons_3.pressed;
              isSqueezedR = inputsourceR.gamepad.buttons_1.pressed;
              controllerColor = [0,0,0];
              if(isSelectedR, controllerColor = controllerColor + [1,0,0]);
              if(isSqueezedR, controllerColor = controllerColor + [0,1,0]);

              //reads the position in homogeneous coordinates
              //homogeneous coordinates end in 1 if tracked, zero if not tracked
              draw3d(inputsourceR.gripSpaceTransform.position, color->controllerColor, size->.5);

              //grab the height of the controller.
              h = take(inputsourceR.gripSpaceTransform.position, 2);
              crossSectSphere(sphereCenter, sphere
          );

        crossSectSphere(sphereCenter, sphereRadius, crossSectPlanePos, crossSectPlaneNorm);
      end3d();
      );
      xr(xrrenderfunction());
  </script>
---
