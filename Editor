using UnityEditor;
using UnityEngine;

[CustomEditor(typeof(BodyPartSO))]
public class BodyPartSOEditor : Editor
{
    private Texture2D _lastTexture;
    private BodyPartSO _target;

    private void OnEnable()
    {
        _target = (BodyPartSO)target;
        _lastTexture = _target.shapeTexture;
    }

    public override void OnInspectorGUI()
    {
        // Draw default inspector properties
        DrawDefaultInspector();

        // Check for texture changes
        if (_target.shapeTexture != _lastTexture)
        {
            _lastTexture = _target.shapeTexture;
            if (_target.shapeTexture != null)
            {
                BakeAndSave();
            }
        }

        // Manual bake button
        EditorGUI.BeginDisabledGroup(_target.shapeTexture == null);
        if (GUILayout.Button("Bake Shape"))
        {
            BakeAndSave();
        }
        EditorGUI.EndDisabledGroup();

        // Texture information section
        if (_target.shapeTexture != null)
        {
            DrawTextureInfo();
        }
        else
        {
            EditorGUILayout.HelpBox("Assign a texture to bake the shape!", MessageType.Warning);
        }
    }

    /// <summary>
    /// Bakes the shape and marks the object as dirty
    /// </summary>
    private void BakeAndSave()
    {
        _target.BakeShape();
        EditorUtility.SetDirty(_target);
        Repaint(); // Refresh the inspector to show updates
    }

    /// <summary>
    /// Draws texture information and preview
    /// </summary>
    private void DrawTextureInfo()
    {
        EditorGUILayout.Space();
        EditorGUILayout.LabelField("Texture Info", EditorStyles.boldLabel);
        EditorGUILayout.LabelField($"Size: {_target.shapeTexture.width}x{_target.shapeTexture.height}");

        // Texture preview with aspect ratio preservation
        float previewSize = EditorGUIUtility.currentViewWidth - 30f;
        float aspectRatio = (float)_target.shapeTexture.height / _target.shapeTexture.width;
        Rect rect = GUILayoutUtility.GetRect(previewSize, previewSize * aspectRatio);
        EditorGUI.DrawPreviewTexture(rect, _target.shapeTexture);
    }
}
