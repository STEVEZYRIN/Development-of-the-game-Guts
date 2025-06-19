using Extensions;
using System.Collections.Generic;
using UnityEngine;

[CreateAssetMenu(fileName = "body-part", menuName = "Creature/Body/Body Part")]
public class BodyPartSO : ScriptableObject
{
    public string partName;
    public float maxHP;
    public List<float> resistance;
    public bool isVital;

    [Header("Organ Shape")]
    public Texture2D shapeTexture;
    public TArray<bool> shapeMatrix;

    [HideInInspector] public Vector2Int originOffset;

#if UNITY_EDITOR
    private void OnValidate()
    {
        BakeShape();
    }
#endif

    public void BakeShape()
    {
        shapeMatrix = new TArray<bool>(0, 0);
        originOffset = Vector2Int.zero;

        if (shapeTexture == null)
        {
            Debug.LogWarning("Texture is not assigned!", this);
            return;
        }

        if (!shapeTexture.isReadable)
        {
            Debug.LogError($"Texture {shapeTexture.name} is not readable! Enable Read/Write in import settings.", this);
            return;
        }

        try
        {
            var dimensions = new Vector2Int(shapeTexture.width, shapeTexture.height);
            var pixels = shapeTexture.GetPixels();
            var tempMatrix = new TArray<bool>(dimensions.x, dimensions.y);

            // Fill matrix (flipped vertically)
            for (int i = 0; i < pixels.Length; i++)
            {
                int x = i % dimensions.x;
                int y = dimensions.y - 1 - (i / dimensions.x);
                tempMatrix[x, y] = pixels[i].a > 0.5f;
            }

            Debug.Log($"Organ shape «{partName}» baked. Size: {dimensions.x}x{dimensions.y}", this);

            // Find bounds
            int left = dimensions.x, right = 0, top = 0, bottom = dimensions.y;
            bool hasPixels = false;

            for (int y = 0; y < dimensions.y; y++)
            {
                for (int x = 0; x < dimensions.x; x++)
                {
                    if (tempMatrix[x, y])
                    {
                        hasPixels = true;
                        if (x < left) left = x;
                        if (x > right) right = x;
                        if (y > top) top = y;
                        if (y < bottom) bottom = y;
                    }
                }
            }

            if (!hasPixels)
            {
                Debug.LogWarning($"Organ shape «{partName}» is empty!", this);
                return;
            }

            // Create optimized matrix
            int newWidth = right - left + 1;
            int newHeight = top - bottom + 1;
            var optimized = new TArray<bool>(newWidth, newHeight);

            for (int y = 0; y < newHeight; y++)
            {
                for (int x = 0; x < newWidth; x++)
                {
                    optimized[x, y] = tempMatrix[left + x, bottom + y];
                }
            }

            originOffset = new Vector2Int(left, bottom);
            shapeMatrix = optimized;

            Debug.Log($"Optimized «{partName}»: {dimensions.x}x{dimensions.y} → {newWidth}x{newHeight}", this);
        }
        catch (UnityException e)
        {
            Debug.LogError($"Texture reading error: {e.Message}", this);
        }
    }
}
