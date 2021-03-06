# gframeAE
A Java 3D engine for Android applications. Uses a pure software renderer instead of an OpenGL implementation for broader availability. Original Java sources can be found at the [gframe repository](https://github.com/conradplake/gframe).

Features include:
 - pixel-perfect perspective correct texture mapping
 - sub-pixel & sub-texel accuracy
 - light attenuation
 - flat & phong shading
 - normal & specular mapping
 - shadow mapping
 - physics support & particles



As a first hands-on, here is an example Android View-Class that shows a single rotating cube:

```java
import android.content.Context;
import android.graphics.Canvas;
import android.util.AttributeSet;
import android.view.View;
import gframe.ae.engine.Engine3D;
import gframe.ae.engine.Lightsource;
import gframe.ae.engine.Material;
import gframe.ae.engine.Model3D;
import gframe.ae.engine.NormalMappedMaterialShader;
import gframe.ae.engine.Shader;
import gframe.ae.engine.camera.Camera;
import gframe.ae.engine.camera.TripodCamera;
import gframe.ae.engine.generator.Model3DGenerator;
import gframe.ae.engine.generator.TextureGenerator;
import gframe.ae.engine.timing.Rotate;
import gframe.ae.engine.timing.Timer;

public class MyView extends View {

    private Engine3D engine;

    public MyView(Context context) {
        super(context);
    }

    public MyView(Context context, AttributeSet attrs) {
        super(context, attrs);
    }

    public MyView(Context context, AttributeSet attrs, int defStyle) {
        super(context, attrs, defStyle);
    }

    private void setupEngineAndWorld() {
        if (engine != null) {
            engine.clear();
        }

        engine = new Engine3D(getWidth(), getHeight());

        Lightsource lightsource = new Lightsource(0, 0, 0, gframe.ae.engine.Color.white, Lightsource.MAX_INTENSITY);
        engine.setLightsource(lightsource);

        Shader shader = new NormalMappedMaterialShader(lightsource, TextureGenerator.generateTileTextureNormalMap(256, 256, 32));
        engine.setDefaultShader(shader);

        // add something to the world..
        Model3D model = Model3DGenerator.buildBlock(80, 80, 80, gframe.ae.engine.Color.white);
        model = Model3DGenerator.facify(model);
        model.rotate(-45, -45, 45);  
        model.setMaterial(Material.GOLD);
        
        engine.register(model);

        // let object rotate
        Timer timer = Timer.getInstance();
        Rotate rotate = new Rotate(model, 100000000, 0.01f, Rotate.AXIS_Z);
        timer.registerTimedObject(rotate);

        // light and camera settings
        lightsource.setCoordinates(0, 0, -100);

        Camera camera = new TripodCamera();
        camera.move(0, 0, -200);
        engine.setCamera(camera);
    }

    @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);

        if (engine == null) {
            setupEngineAndWorld();
        }

        engine.drawScene(canvas);

        invalidate();
    }

}
```

Should look like so:

![alt text](https://github.com/conradplake/gframeAE/blob/master/TestApp_Screenshot.png "Test application screenshot") ![alt text](https://github.com/conradplake/gframeAE/blob/master/TestApp_Screenshot2.png "Test application screenshot")
