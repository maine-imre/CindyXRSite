---
layout: recipe
title:  "Shearing Triangle"
image:  triangle.png
script: >
    <script id="init" type="text/x-cindyscript">
        use("Cindy3D");
        use("CindyXR");
        initxrcindy3d(referencemode->"local-floor");
    
        tolerance = 0.03;
    
        vertices =
          [
            [1,.5,0,1],
            [0,.5,0,1],
            [0,1.5,0,1]
          ];
        lineSlope = vertices_2-vertices_1;
    
        selectButton = dict(left->0, right->0);
        squeezeButton = dict(left->0, right->0);
    </script>
    
    <script id="xrdraw" type="text/x-cindyscript">
        //Function Definitions:
        draw3Dxrinputsources(xrinputsources) :=(
            repeat(length(xrinputsources), i,
                inputsource = xrinputsources_i;
                print(inputsource.profiles);
                // We only want input sources regarding tracked controllers.
                if (inputsource.targetRayMode == "tracked-pointer",
                    // Draw a ball at the position of the controller and color it depending on whether button 1 is pressed.
                    handedness = inputsource.handedness;
                    draw3d(inputsource.gripSpaceTransform.position, size -> 0.3, color ->[get(selectButton,handedness),get(squeezeButton,handedness),(0.0)]);
                );
            );
         );
    
        draw3Dline(point, slope) :=(
            //this should eventually be indefinate, instead of 100m
            draw3d(point+1000*slope,point-1000*slope,size->.1, color->[.7,.7,.7]);
        );
    
        draw3Dtriangle(vertexList) :=(
          //draw vertices
          repeat(length(vertices),i,draw3d(vertexList_i, size->.3, color->[1,0,0]));
    
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
    
        //draw triangle abc
        draw3Dtriangle(vertices);
    
        //draw parallel lines
            draw3Dline(vertices_1,lineSlope); //line thur ab
            draw3Dline(vertices_3,lineSlope); //parallel thru c
    
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
        controllerPosition = inputsource.gripSpaceTransform.position;
        repeat(length(vertices), i,
            print(controllerPosition);
            print(vertices_(i));
            closeEnough = |(controllerPosition - vertices_(i))| < tolerance;
            if(closeEnough,
                vertices_i = projectOntoLine(controllerPosition, vertices_i, lineSlope);
            );
        );
    </script>
    
    <script id="xrselectstart" type="text/x-cindyscript">
      selectButton = put(selectButton, inputsource.handedness, 1);
    </script>
    
    <script id="xrselectend" type="text/x-cindyscript">
      selectButton = put(selectButton, inputsource.handedness, 0);
    </script>
    
    
    <script id="xrsqueezehold" type="text/x-cindyscript">
        //drag the verticies when proximal and holding the squeeze button
        controllerPosition = inputsource.gripSpaceTransform.position;
        repeat(length(vertices), i,
            closeEnough = |(controllerPosition - vertices_(i))| < tolerance;
            if(closeEnough,
                vertices_i = controllerPosition;
                lineSlope = vertices_2-vertices_1;
            );
        );
    </script>
    
    <script id="xrsqueezestart" type="text/x-cindyscript">
      selectButton = put(selectButton, inputsource.handedness, 1);
    </script>
    
    <script id="xrsqueezeend" type="text/x-cindyscript">
      selectButton = put(squeezeButton, inputsource.handedness, 0);
    </script>
    
    

---
