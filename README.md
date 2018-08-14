# gframeAE
A Java 3D engine for Android applications

Features include:
 - pixel-perfect perspective correct texture mapping
 - sub-pixel & sub-texel accuracy
 - flat & phong shading
 - normal & specular mapping
 - shadow mapping
 - physics support & particles



As a small tutorial here is an example View-Class that shows a single rotating cube.

```java
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
        model.scale(1, 1, 1);
        model.setMaterial(Material.GOLD);
        
        engine.register(model);

        // let object rotate
        Timer timer = Timer.getInstance();
        Rotate rotate = new Rotate(model, 100000000, 0.01f, Rotate.AXIS_Z);
        timer.registerTimedObject(rotate);

        // LIGHT + CAMERA SETTINGS
        lightsource.x = 0;
        lightsource.y = 0;
        lightsource.z = -100;

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

