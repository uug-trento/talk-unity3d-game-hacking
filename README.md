# talk-unity3d-game-hacking
Slides for the talk in the UUGTrento #8 
## Links
Decompilers:
* https://github.com/0xd4d/dnSpy
* https://github.com/icsharpcode/ILSpy

Deobfuscator:
* https://github.com/0xd4d/de4dot

## Demo Code
### ESP
```csharp
Material lineMaterial;
public void OnRenderObject()
{
    if (this.firstPersonMode || Camera.current.name != "FpsCamera" || !Character.CHEAT_ESP)
    {
        return;
    }
    if (!this.lineMaterial)
    {
        this.lineMaterial = new Material(Shader.Find("GUI/Text Shader"));
        this.lineMaterial.color = Color.white;
        this.lineMaterial.hideFlags = HideFlags.HideAndDontSave;
        this.lineMaterial.SetInt("_SrcBlend", 5);
        this.lineMaterial.SetInt("_DstBlend", 10);
        this.lineMaterial.SetInt("_Cull", 0);
        this.lineMaterial.SetInt("_ZWrite", 0);
        this.lineMaterial.SetInt("_ZTest", 0);
    }
    this.lineMaterial.SetPass(0);
    GL.PushMatrix();
    GL.MultMatrix(base.transform.localToWorldMatrix);
    GL.Begin(1);
    GL.Color(Color.red);
    float height = (IsCrouched ? 2 : 2.5f);
    GL.Vertex(new Vector3(0.5f, 0f));
    GL.Vertex(new Vector3(0.5f, height));
    GL.Vertex(new Vector3(0.5f, height));
    GL.Vertex(new Vector3(-0.5f, height));
    GL.Vertex(new Vector3(-0.5f, height));
    GL.Vertex(new Vector3(-0.5f, 0f));
    GL.Vertex(new Vector3(-0.5f, 0f));
    GL.Vertex(new Vector3(0.5f, 0f));
    GL.Color(Color.green);
    GL.Vertex(new Vector3(-0.5f, y+0.1f));
    GL.Vertex(new Vector3(-0.5f + this.GetVirtualHealthPercentage(), y+0.1f));
    GL.Color(Color.white);
    GL.Vertex(new Vector3(-0.5f + this.GetVirtualHealthPercentage(), y+0.1f));
    GL.Vertex(new Vector3(0.5f, y+0.1f));
    GL.End();
    GL.PopMatrix();
}
```
### Fly hack
```csharp
bool flyEnabled;

//Disable gravity when the hack is active
private void Move(float deltaTime)
{
	//[...]
	this.speed.y = this.speed.y + Physics.gravity.y * deltaTime * (flyEnabled ? 0 : 1);
	//[...]
}
```

### Aimbot
```csharp
FirstPersonCamera.cs
void vmethod_2()
{
	//[...] if (character.IsUserControlled())
	if (Input.GetMouseButton(2))
	{
		Character[] array = UnityEngine.Object.FindObjectsOfType<Character>();
		float minDist = float.maxValue;
		Character nearest = null;
		foreach (Character o in array)
		{
			if (!o.FirstPersonMode)
			{
				float dist = Vector3.Distance(o.transform.position, transform.position);
				if (dist < minDist)
				{
					minDist = dist;
					nearest = o;
				}
			}
		}
		if (nearest)
		{
			transform.LookAt(nearest.transform.position + new Vector3(0f, 2.25f));
		}
	}
}
```
CHEAT MENU
```csharp
private void OnGUI()
	{
		if (GUI.Button(new Rect(10f, 10f, 150f, 50f), "ESP: " + (Character.CHEAT_ESP ? "ON" : "OFF")) || Input.GetKeyDown(KeyCode.I))
		{
			Character.CHEAT_ESP = !Character.CHEAT_ESP;
		}
		if (GUI.Button(new Rect(10f, 60f, 150f, 50f), "AIMBOT: " + (Character.CHEAT_AIMBOT ? "ON" : "OFF")) || Input.GetKeyDown(KeyCode.P))
		{
			Character.CHEAT_AIMBOT = !Character.CHEAT_AIMBOT;
		}
		if (GUI.Button(new Rect(10f, 110f, 150f, 50f), "NO GRAVITY: " + (this.flyEnabled ? "ON" : "OFF")) || Input.GetKeyDown(KeyCode.O))
		{
			this.flyEnabled = !this.flyEnabled;
		}
	}
```
