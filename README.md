# talk-unity3d-game-hacking
Slides for the talk in the UUGTrento #8 

## Demo Code
### ESP
```csharp
Material lineMaterial;
public void OnRenderObject()
{
    if (this.firstPersonMode)
    {
        return;
    }
    if (!this.lineMaterial)
    {
        this.lineMaterial = new Material(Shader.Find("GUI/Text Shader"));
        this.lineMaterial.color = Color.red;
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
    GL.Vertex(new Vector3(0.5f, 0f));
    GL.Vertex(new Vector3(0.5f, 2f));
    GL.Vertex(new Vector3(0.5f, 2f));
    GL.Vertex(new Vector3(-0.5f, 2f));
    GL.Vertex(new Vector3(-0.5f, 2f));
    GL.Vertex(new Vector3(-0.5f, 0f));
    GL.Vertex(new Vector3(-0.5f, 0f));
    GL.Vertex(new Vector3(0.5f, 0f));
    GL.End();
    GL.PopMatrix();
}
```
### Fly hack
```csharp
bool flyEnabled;

void FlyHack()
{
	if (Input.GetKeyDown(KeyCode.O))
		flyEnabled = !flyEnabled;
}

//Add FlyHack to Update
void Update()
{
	FlyHack();
	//[...]
}

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
			float maxDist = -1f;
			Character nearest = null;
			foreach (Character o in array)
			{
				if (!o.FirstPersonMode)
				{
					float dist = Vector3.Distance(o.transform.position, transform.position);
					if (dist > maxDist)
					{
						maxDist = dist;
						nearest = o;
					}
				}
			}
			if (nearest)
			{
				transform.LookAt(nearest.transform);
			}
		}
}
```
