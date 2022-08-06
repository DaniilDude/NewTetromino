# NewTetromino
СЛАВА УКРАИНЕ

Figure.cs

using System.Collections.Generic;
using UnityEngine;


public class Figure : UniqueComponentAtObject
{
    public enum Types
    {
        None,
        O,
        I,
        S,
        Z,
        L,
        J,
        T
    }


    // ===========================================================================================
#if UNITY_EDITOR
    [ReadOnly, SerializeField, Tooltip("Maximum number of blocks in figure")]
    private int _figureBlocksCount = FigureBlocksCount;
#endif
    public const int FigureBlocksCount = 16;
#if UNITY_EDITOR
    [ReadOnly, SerializeField, Tooltip("Maximum number of blocks in a lines, horizontal and vertical")]
    private int _lineBlocksCount = LineBlocksCount;
#endif
    public const int LineBlocksCount = 4;
    public const float HalfBoundSize = LineBlocksCount / 2F;

    [ReadOnly, SerializeField]
    private int _positionX;
    public int PositionX
    {
        get { return _positionX; }
        set
        {
            _positionX = value;
            transform.localPosition = new Vector3(_positionX, _positionY);
        }
    }
    [ReadOnly, SerializeField]
    private int _positionY;
    public int PositionY
    {
        get { return _positionY; }
        set
        {
            _positionY = value;
            transform.localPosition = new Vector3(_positionX, _positionY);
        }
    }
    [ReadOnly, SerializeField]
    private Types _figureType;
    public Types FigureType {
        get { return _figureType; }
        private set { _figureType = value; }
    }
    public float VisibleSizeX { get; private set; }
    public float VisibleSizeY { get; private set; }
    public int Rotation { get; private set; }
    public SquareBlock OTypeBlockPrefab;
    public SquareBlock ITypeBlockPrefab;
    public SquareBlock STypeBlockPrefab;
    public SquareBlock ZTypeBlockPrefab;
    public SquareBlock LTypeBlockPrefab;
    public SquareBlock JTypeBlockPrefab;
    public SquareBlock TTypeBlockPrefab;
    public SquareBlock ShadowBlockPrefab;
    public SquareBlock[] Blocks = new SquareBlock[LineBlocksCount * LineBlocksCount];

    private SquareBlock _blockPrefab;



    // ===========================================================================================
    public Figure Destroy()
    {
        if (Application.isEditor && !Application.isPlaying)
            DestroyImmediate(gameObject);
        else
            Destroy(gameObject);

        return null;
    }

    public void Clear(bool animate)
    {
        _blockPrefab = null;
        Rotation = 0;

        for (int i = 0; i < FigureBlocksCount; i++)
            Blocks[i] = null;

        var b = GetComponentsInChildren<SquareBlock>(true);
        for (int i = b.Length - 1; i >= 0; i--)
            b[i].Destroy(animate);

        FigureType = Types.None;
    }

    public void CreateRandom()
    {
        var t = 1 + new System.Random().Next() % 7;
        Create((Types)t);
    }

    public void Create(Types fType, bool isShadow = false)
    {
        Clear(!isShadow);

        switch (fType)
        {
            case Types.O:
                _blockPrefab = !isShadow ? OTypeBlockPrefab : ShadowBlockPrefab;
                Blocks[5] = NewBlock(1, 1);
                Blocks[6] = NewBlock(2, 1);
                Blocks[9] = NewBlock(1, 2);
                Blocks[10] = NewBlock(2, 2);
                VisibleSizeX = 2F; VisibleSizeY = 2F;
                break;
            case Types.I:
                _blockPrefab = !isShadow ? ITypeBlockPrefab : ShadowBlockPrefab;
                Blocks[8] = NewBlock(0, 2);
                Blocks[9] = NewBlock(1, 2);
                Blocks[10] = NewBlock(2, 2);
                Blocks[11] = NewBlock(3, 2);
                VisibleSizeX = 4F; VisibleSizeY = 1F;
                break;
            case Types.S:
                _blockPrefab = !isShadow ? STypeBlockPrefab : ShadowBlockPrefab;
                Blocks[5] = NewBlock(1, 1);
                Blocks[6] = NewBlock(2, 1);
                Blocks[10] = NewBlock(2, 2);
                Blocks[11] = NewBlock(3, 2);
                VisibleSizeX = 3F; VisibleSizeY = 2F;
                break;
            case Types.Z:
                _blockPrefab = !isShadow ? ZTypeBlockPrefab : ShadowBlockPrefab;
                Blocks[6] = NewBlock(2, 1);
                Blocks[7] = NewBlock(3, 1);
                Blocks[9] = NewBlock(1, 2);
                Blocks[10] = NewBlock(2, 2);
                VisibleSizeX = 3F; VisibleSizeY = 2F;
                break;
            case Types.L:
                _blockPrefab = !isShadow ? LTypeBlockPrefab : ShadowBlockPrefab;
                Blocks[5] = NewBlock(1, 1);
                Blocks[9] = NewBlock(1, 2);
                Blocks[10] = NewBlock(2, 2);
                Blocks[11] = NewBlock(3, 2);
                VisibleSizeX = 3F; VisibleSizeY = 2F;
                break;
            case Types.J:
                _blockPrefab = !isShadow ? JTypeBlockPrefab : ShadowBlockPrefab;
                Blocks[7] = NewBlock(3, 1);
                Blocks[9] = NewBlock(1, 2);
                Blocks[10] = NewBlock(2, 2);
                Blocks[11] = NewBlock(3, 2);
                VisibleSizeX = 3F; VisibleSizeY = 2F;
                break;
            case Types.T:
                _blockPrefab = !isShadow ? TTypeBlockPrefab : ShadowBlockPrefab;
                Blocks[6] = NewBlock(2, 1);
                Blocks[9] = NewBlock(1, 2);
                Blocks[10] = NewBlock(2, 2);
                Blocks[11] = NewBlock(3, 2);
                VisibleSizeX = 3F; VisibleSizeY = 2F;
                break;
        }

        FigureType = fType;
    }

    public void RotateClockwise()
    {
        switch (FigureType)
        {
            case Types.O:
                return;
            case Types.I:
                RotateAnticlockwise();
                return;
            case Types.S:
                RotateAnticlockwise();
                return;
            case Types.Z:
                RotateAnticlockwise();
                return;
            case Types.L:
                switch (Rotation)
                {
                    case 0:
                        Blocks[13] = Blocks[5];
                        Blocks[5] = null;
                        Blocks[14] = Blocks[9];
                        Blocks[9] = null;
                        Blocks[10] = Blocks[10];
                        Blocks[6] = Blocks[11];
                        Blocks[11] = null;
                        Rotation = 3;
                        break;
                    case 1:
                        Blocks[5] = Blocks[7];
                        Blocks[7] = null;
                        Blocks[9] = Blocks[6];
                        Blocks[6] = null;
                        Blocks[10] = Blocks[10];
                        Blocks[11] = Blocks[14];
                        Blocks[14] = null;
                        Rotation = 0;
                        break;
                    case 2:
                        Blocks[7] = Blocks[15];
                        Blocks[15] = null;
                        Blocks[6] = Blocks[11];
                        Blocks[11] = null;
                        Blocks[10] = Blocks[10];
                        Blocks[14] = Blocks[9];
                        Blocks[9] = null;
                        Rotation = 1;
                        break;
                    case 3:
                        Blocks[15] = Blocks[13];
                        Blocks[13] = null;
                        Blocks[11] = Blocks[14];
                        Blocks[14] = null;
                        Blocks[10] = Blocks[10];
                        Blocks[9] = Blocks[6];
                        Blocks[6] = null;
                        Rotation = 2;
                        break;
                }
                break;
            case Types.J:
                switch (Rotation)
                {
                    case 0:
                        Blocks[5] = Blocks[7];
                        Blocks[7] = null;
                        Blocks[6] = Blocks[11];
                        Blocks[11] = null;
                        Blocks[10] = Blocks[10];
                        Blocks[14] = Blocks[9];
                        Blocks[9] = null;
                        Rotation = 3;
                        break;
                    case 1:
                        Blocks[7] = Blocks[15];
                        Blocks[15] = null;
                        Blocks[11] = Blocks[14];
                        Blocks[14] = null;
                        Blocks[10] = Blocks[10];
                        Blocks[9] = Blocks[6];
                        Blocks[6] = null;
                        Rotation = 0;
                        break;
                    case 2:
                        Blocks[15] = Blocks[13];
                        Blocks[13] = null;
                        Blocks[14] = Blocks[9];
                        Blocks[9] = null;
                        Blocks[10] = Blocks[10];
                        Blocks[6] = Blocks[11];
                        Blocks[11] = null;
                        Rotation = 1;
                        break;
                    case 3:
                        Blocks[13] = Blocks[5];
                        Blocks[5] = null;
                        Blocks[9] = Blocks[6];
                        Blocks[6] = null;
                        Blocks[10] = Blocks[10];
                        Blocks[11] = Blocks[14];
                        Blocks[14] = null;
                        Rotation = 2;
                        break;
                }
                break;
            case Types.T:
                switch (Rotation)
                {
                    case 0:
                        Blocks[6] = Blocks[6];
                        Blocks[14] = Blocks[11];
                        Blocks[11] = null;
                        Blocks[10] = Blocks[10];
                        Blocks[9] = Blocks[9];
                        Rotation = 3;
                        break;
                    case 1:
                        Blocks[9] = Blocks[14];
                        Blocks[14] = null;
                        Blocks[6] = Blocks[6];
                        Blocks[10] = Blocks[10];
                        Blocks[11] = Blocks[11];
                        Rotation = 0;
                        break;
                    case 2:
                        Blocks[11] = Blocks[11];
                        Blocks[14] = Blocks[14];
                        Blocks[10] = Blocks[10];
                        Blocks[6] = Blocks[9];
                        Blocks[9] = null;
                        Rotation = 1;
                        break;
                    case 3:
                        Blocks[14] = Blocks[14];
                        Blocks[9] = Blocks[9];
                        Blocks[11] = Blocks[6];
                        Blocks[6] = null;
                        Blocks[10] = Blocks[10];
                        Rotation = 2;
                        break;
                }
                break;
        }

        UpdateBlocks();
        SwitchVisibleSizes();
    }

    public void RotateAnticlockwise()
    {
        switch (FigureType)
        {
            case Types.O:
                return;
            case Types.I:
                if (Rotation == 0)
                {
                    Blocks[2] = Blocks[8];
                    Blocks[8] = null;
                    Blocks[6] = Blocks[9];
                    Blocks[9] = null;
                    Blocks[10] = Blocks[10];
                    Blocks[14] = Blocks[11];
                    Blocks[11] = null;
                    Rotation = 1;
                }
                else
                {
                    Blocks[8] = Blocks[2];
                    Blocks[2] = null;
                    Blocks[9] = Blocks[6];
                    Blocks[6] = null;
                    Blocks[10] = Blocks[10];
                    Blocks[11] = Blocks[14];
                    Blocks[14] = null;
                    Rotation = 0;
                }
                break;
            case Types.S:
                if (Rotation == 0)
                {
                    Blocks[7] = Blocks[5];
                    Blocks[5] = null;
                    Blocks[10] = Blocks[10];
                    Blocks[11] = Blocks[11];
                    Blocks[14] = Blocks[6];
                    Blocks[6] = null;
                    Rotation = 1;
                }
                else
                {
                    Blocks[5] = Blocks[7];
                    Blocks[7] = null;
                    Blocks[10] = Blocks[10];
                    Blocks[11] = Blocks[11];
                    Blocks[6] = Blocks[14];
                    Blocks[14] = null;
                    Rotation = 0;
                }
                break;
            case Types.Z:
                if (Rotation == 0)
                {
                    Blocks[6] = Blocks[6];
                    Blocks[15] = Blocks[7];
                    Blocks[7] = null;
                    Blocks[10] = Blocks[10];
                    Blocks[11] = Blocks[9];
                    Blocks[9] = null;
                    Rotation = 1;
                }
                else
                {
                    Blocks[6] = Blocks[6];
                    Blocks[7] = Blocks[15];
                    Blocks[15] = null;
                    Blocks[10] = Blocks[10];
                    Blocks[9] = Blocks[11];
                    Blocks[11] = null;
                    Rotation = 0;
                }
                break;
            case Types.L:
                switch (Rotation)
                {
                    case 0:
                        Blocks[7] = Blocks[5];
                        Blocks[5] = null;
                        Blocks[6] = Blocks[9];
                        Blocks[9] = null;
                        Blocks[10] = Blocks[10];
                        Blocks[14] = Blocks[11];
                        Blocks[11] = null;
                        Rotation = 1;
                        break;
                    case 1:
                        Blocks[15] = Blocks[7];
                        Blocks[7] = null;
                        Blocks[11] = Blocks[6];
                        Blocks[6] = null;
                        Blocks[10] = Blocks[10];
                        Blocks[9] = Blocks[14];
                        Blocks[14] = null;
                        Rotation = 2;
                        break;
                    case 2:
                        Blocks[13] = Blocks[15];
                        Blocks[15] = null;
                        Blocks[14] = Blocks[11];
                        Blocks[11] = null;
                        Blocks[10] = Blocks[10];
                        Blocks[6] = Blocks[9];
                        Blocks[9] = null;
                        Rotation = 3;
                        break;
                    case 3:
                        Blocks[5] = Blocks[13];
                        Blocks[13] = null;
                        Blocks[9] = Blocks[14];
                        Blocks[14] = null;
                        Blocks[10] = Blocks[10];
                        Blocks[11] = Blocks[6];
                        Blocks[6] = null;
                        Rotation = 0;
                        break;
                }
                break;
            case Types.J:
                switch (Rotation)
                {
                    case 0:
                        Blocks[15] = Blocks[7];
                        Blocks[7] = null;
                        Blocks[14] = Blocks[11];
                        Blocks[11] = null;
                        Blocks[10] = Blocks[10];
                        Blocks[6] = Blocks[9];
                        Blocks[9] = null;
                        Rotation = 1;
                        break;
                    case 1:
                        Blocks[13] = Blocks[15];
                        Blocks[15] = null;
                        Blocks[9] = Blocks[14];
                        Blocks[14] = null;
                        Blocks[10] = Blocks[10];
                        Blocks[11] = Blocks[6];
                        Blocks[6] = null;
                        Rotation = 2;
                        break;
                    case 2:
                        Blocks[5] = Blocks[13];
                        Blocks[13] = null;
                        Blocks[6] = Blocks[9];
                        Blocks[9] = null;
                        Blocks[10] = Blocks[10];
                        Blocks[14] = Blocks[11];
                        Blocks[11] = null;
                        Rotation = 3;
                        break;
                    case 3:
                        Blocks[7] = Blocks[5];
                        Blocks[5] = null;
                        Blocks[11] = Blocks[6];
                        Blocks[6] = null;
                        Blocks[10] = Blocks[10];
                        Blocks[9] = Blocks[14];
                        Blocks[14] = null;
                        Rotation = 0;
                        break;
                }
                break;
            case Types.T:
                switch (Rotation)
                {
                    case 0:
                        Blocks[6] = Blocks[6];
                        Blocks[14] = Blocks[9];
                        Blocks[9] = null;
                        Blocks[10] = Blocks[10];
                        Blocks[11] = Blocks[11];
                        Rotation = 1;
                        break;
                    case 1:
                        Blocks[9] = Blocks[6];
                        Blocks[6] = null;
                        Blocks[14] = Blocks[14];
                        Blocks[10] = Blocks[10];
                        Blocks[11] = Blocks[11];
                        Rotation = 2;
                        break;
                    case 2:
                        Blocks[9] = Blocks[9];
                        Blocks[14] = Blocks[14];
                        Blocks[10] = Blocks[10];
                        Blocks[6] = Blocks[11];
                        Blocks[11] = null;
                        Rotation = 3;
                        break;
                    case 3:
                        Blocks[6] = Blocks[6];
                        Blocks[9] = Blocks[9];
                        Blocks[11] = Blocks[14];
                        Blocks[14] = null;
                        Blocks[10] = Blocks[10];
                        Rotation = 0;
                        break;
                }
                break;
        }

        UpdateBlocks();
        SwitchVisibleSizes();
    }

    public void RotateTo(int newRotation)
    {
        if (Rotation == newRotation)
            return;

        if (newRotation < 0 || newRotation > 3)
            RotateTo(newRotation % 4);
        else
        {
            var count = 4 - Mathf.Abs(Rotation - newRotation);
            for (int i = 0; i < count; i++)
                RotateAnticlockwise();
        }
    }

    public void MoveCenterToParentOrigin()
    {
        transform.localPosition = -new Vector3(HalfBoundSize + VisibleSizeX % 2F / 2F
                   , HalfBoundSize + VisibleSizeY % 2F / 2F
                   , 0F);
    }

    protected override void Awake()
    {
        base.Awake();

        name = "Figure" + GetInstanceID();
    }

    protected void OnEnable()
    {
        if (FigureType == Types.None)
        {
            CreateRandom();
            MoveCenterToParentOrigin();
        }
    }


    // ===========================================================================================
    private SquareBlock NewBlock(int x, int y)
    {
        var newBlock = _blockPrefab != null ? Instantiate(_blockPrefab) : new GameObject().AddComponent<SquareBlock>();
        newBlock.name = string.Format("B{0}{1}", x, y);
        newBlock.transform.parent = transform;
        newBlock.transform.localScale = Vector3.one;
        newBlock.LocalPositionX = x;
        newBlock.LocalPositionY = y;

        return newBlock;
    }

    private void UpdateBlocks()
    {
        int indx;
        for (int y = 0; y < LineBlocksCount; y++)
            for (int x = 0; x < LineBlocksCount; x++)
            {
                indx = y*LineBlocksCount + x;
                if (Blocks[indx] != null)
                {
                    Blocks[indx].name = string.Format("B{0}{1}", x, y);
                    Blocks[indx].LocalPositionX = x;
                    Blocks[indx].LocalPositionY = y;
                }
            }
    }

    private void SwitchVisibleSizes()
    {
        var temp = VisibleSizeX;
        VisibleSizeX = VisibleSizeY;
        VisibleSizeY = temp;
    }
}

Сolision.cs

using System;
using System.Linq;
using UnityEngine;
#if UNITY_EDITOR
using UnityEditor;
#endif


public static class ObjectExtensionMethods
{
    public static UnityEngine.Object[] FindObjectsOfTypeAtSceneAll(this UnityEngine.Object target, Type type)
    {
        var result = Resources.FindObjectsOfTypeAll(type);
#if UNITY_EDITOR
        result = result.Where(x => !EditorUtility.IsPersistent(x)).ToArray();
#endif
        return result;
    }

    public static UnityEngine.Object[] FindObjectsOfTypeAtSceneAll<T>(this UnityEngine.Object target)
    {
        return FindObjectsOfTypeAtSceneAll(target, typeof (T));
    }
}

GameField

using System.Collections;
using System.Linq;
using System.Collections.Generic;
using UnityEngine;


[AddComponentMenu("Unique/GameField")]
[RequireComponent(typeof(SpriteRenderer))]
public class GameField : UniqueComponentAtScene<GameField>
{
    // ===========================================================================================
#if UNITY_EDITOR
    [ReadOnly, SerializeField]
    private int _blocksXCount = BlocksXCount;
#endif
    public const int BlocksXCount = 10;
#if UNITY_EDITOR
    [ReadOnly, SerializeField]
    private int _blocksYCount = BlocksYCount;
#endif
    public const int BlocksYCount = 20;
#if UNITY_EDITOR
    [ReadOnly, SerializeField]
    private int _nextFigureCount = NextFigureCount;
#endif
    public const int NextFigureCount = 3;

    public bool IsGameOver { get; private set; }
    public Figure FigurePrefab;
    [ReadOnly, SerializeField]
    public Figure CurrentFigure;
    [ReadOnly, SerializeField]
    public List<Figure> NextFigure = new List<Figure>(NextFigureCount);
    [ReadOnly, SerializeField]
    public Figure HoldFigure;
    [ReadOnly, SerializeField]
    public Figure ShadowFigure;
    public List<Transform> NextFigureSpawnPoint = new List<Transform>(NextFigureCount);
    public Transform HoldFigurePoint;
    public RestartLevelMenu RestartMenu;
    public AudioClip MainAudioTheme;
    public AudioClip RemoveLineSound;
    public AudioClip HoldErrorSound;
    public SquareBlock[] Blocks = new SquareBlock[BlocksXCount*BlocksYCount];

    private SpriteRenderer _renderer;
    private AudioSource _mainAudioThemePlayer;
    private AudioSource _removeLineSoundPlayer;
    private AudioSource _holdErrorPlayer;

    private FigureInput _figureInput;
    private LevelSettings _levelSettings;
    private SlowMo _slowMo;
    private BestScore _bestScore;

    private bool _isReady;
    private bool _figureSwapped;


    // ===========================================================================================
    public void Clear()
    {
        _isReady = false;
        IsGameOver = false;
        _figureInput.ResetTimers();
        _levelSettings.Clear();
        _slowMo.Clear();

        for (int i = 0; i < BlocksXCount*BlocksYCount; i++)
            Blocks[i] = null;

        var b = GetComponentsInChildren<SquareBlock>(true);
        for (int i = b.Length - 1; i >= 0; i--)
            b[i].Destroy(_levelSettings.GlobalEffectsSwitch);

        for (int i = 0; i < NextFigureCount; i++)
            NextFigure[i] = NextFigure[i] != null ? NextFigure[i].Destroy() : null;

        CurrentFigure = CurrentFigure != null ? CurrentFigure.Destroy() : null;
        HoldFigure = HoldFigure != null ? HoldFigure.Destroy() : null;
        ShadowFigure = ShadowFigure != null ? ShadowFigure.Destroy() : null;

        _renderer.sortingLayerName = "Background";
    }

    public void Create()
    {
        Clear();

        for (int i = 0; i < NextFigureCount; i++)
            NextFigure[i] = NewNextFigure(i);

        _mainAudioThemePlayer.Play();
        _isReady = true;
    }

    protected override void Awake()
    {
        base.Awake();

        _renderer = GetComponent<SpriteRenderer>();

        if (RestartMenu == null)
        {
            var menus = this.FindObjectsOfTypeAtSceneAll<RestartLevelMenu>().Cast<RestartLevelMenu>().ToArray();
            if (menus.Length == 1)
                RestartMenu = menus[0];
        }

        if (Application.isEditor && !Application.isPlaying) return;

        _mainAudioThemePlayer = gameObject.AddComponent<AudioSource>();
        _mainAudioThemePlayer.clip = MainAudioTheme;
        _mainAudioThemePlayer.loop = true;
        _mainAudioThemePlayer.volume = 0.7F;

        _removeLineSoundPlayer = gameObject.AddComponent<AudioSource>();
        _removeLineSoundPlayer.clip = RemoveLineSound;
        _removeLineSoundPlayer.priority = 0;

        _holdErrorPlayer = gameObject.AddComponent<AudioSource>();
        _holdErrorPlayer.clip = HoldErrorSound;
        _holdErrorPlayer.priority = 0;
    }

    protected void Start()
    {
        _figureInput = FigureInput.Instance;
        _levelSettings = LevelSettings.Instance;
        _slowMo = SlowMo.Instance;
        _bestScore = BestScore.Instance;

        if (!Application.isPlaying)
            Clear();
        else
            Create();
    }

    protected void Update()
    {
        if (!Application.isPlaying || !_isReady || IsGameOver) return;

        if (CurrentFigure == null)
        {
            if (Blocks[(BlocksYCount - 1) * BlocksXCount + BlocksXCount / 2] != null)
            {
                IsGameOver = true;
                _bestScore.Add(new BestScoreRecord(_levelSettings.PlayerName, _levelSettings.Score));
                RestartMenu.Show();
                return;
            }
            CurrentFigure = NextFigure[0];
            CurrentFigure.transform.SetParent(null, false);
            CurrentFigure.PositionX = BlocksXCount / 2 - Figure.LineBlocksCount / 2;
            CurrentFigure.PositionY = BlocksYCount - Figure.LineBlocksCount + 1;
            NextFigure[0] = null;
            _figureInput.ResetTimers();
            _figureSwapped = false;
        }

        if (NextFigure[0] == null)
        {
            for (int i = 1; i < NextFigureCount; i++)
            {
                NextFigure[i - 1] = NextFigure[i];
                if (NextFigureSpawnPoint[i - 1] != null)
                {
                    NextFigure[i - 1].transform.SetParent(NextFigureSpawnPoint[i - 1], false);
                    NextFigure[i - 1].MoveCenterToParentOrigin();
                }
            }

            NextFigure[NextFigureCount - 1] = NewNextFigure(NextFigureCount - 1);
        }

        if (_levelSettings.IsPause) return;

        if (hardInput.GetKeyDown("Hold"))
        {
            if (_figureSwapped)
            {
                _holdErrorPlayer.Play();
            }
            else
            {
                _figureSwapped = true;

                if (HoldFigure == null)
                {
                    HoldFigure = CurrentFigure;
                    HoldFigure.transform.SetParent(HoldFigurePoint, false);
                    HoldFigure.RotateTo(0);
                    HoldFigure.MoveCenterToParentOrigin();
                    CurrentFigure = null;
                    ShadowFigure = ShadowFigure != null ? ShadowFigure.Destroy() : null;
                    return;
                }

                var temp = CurrentFigure;
                CurrentFigure = HoldFigure;
                HoldFigure = temp;

                HoldFigure.transform.SetParent(HoldFigurePoint, false);
                HoldFigure.RotateTo(0);
                HoldFigure.MoveCenterToParentOrigin();
                ShadowFigure = ShadowFigure != null ? ShadowFigure.Destroy() : null;

                CurrentFigure.transform.SetParent(null, false);
                CurrentFigure.PositionX = BlocksXCount/2 - Figure.LineBlocksCount/2;
                CurrentFigure.PositionY = BlocksYCount - Figure.LineBlocksCount + 1;
                _figureInput.ResetTimers();
            }
        }

        if (ShadowFigure == null)
        {
            ShadowFigure = Instantiate(FigurePrefab);
            ShadowFigure.Create(CurrentFigure.FigureType, true);
            ShadowFigure.gameObject.SetActive(_levelSettings.DrawShadow);
        }

        if (_figureInput.ApplyInputFor(CurrentFigure))
            FinalizeCurrentFigure();
        else
            UpdateShadow();

        CheckLines();
    }

    protected override void OnDestroy()
    {
        if (Application.isEditor && !Application.isPlaying)
        {
            DestroyImmediate(_mainAudioThemePlayer);
            DestroyImmediate(_removeLineSoundPlayer);
            DestroyImmediate(_holdErrorPlayer);
        }
        else
        {
            Destroy(_mainAudioThemePlayer);
            Destroy(_removeLineSoundPlayer);
            Destroy(_holdErrorPlayer);
        }

        _mainAudioThemePlayer = null;
        _removeLineSoundPlayer = null;
        _holdErrorPlayer = null;

        base.OnDestroy();
    }


    // ===========================================================================================
    private Figure NewNextFigure(int index)
    {
        var result = Instantiate(FigurePrefab);

        if (NextFigureSpawnPoint[index] != null)
            result.transform.SetParent(NextFigureSpawnPoint[index], false);

        return result;
    }

    private void UpdateShadow()
    {
        if (ShadowFigure == null)
            return;

        if (_levelSettings.DrawShadow)
        {
            if (!ShadowFigure.gameObject.activeSelf)
                ShadowFigure.gameObject.SetActive(true);

            ShadowFigure.PositionX = CurrentFigure.PositionX;
            ShadowFigure.PositionY = CurrentFigure.PositionY;
            ShadowFigure.RotateTo(CurrentFigure.Rotation);
            FigureInput.Drop(ShadowFigure);
        }
        else
        {
            if (ShadowFigure.gameObject.activeSelf)
                ShadowFigure.gameObject.SetActive(false);
        }
    }

    private void FinalizeCurrentFigure()
    {
        if (CurrentFigure == null) return;

        var currentFigureBlocks = CurrentFigure.Blocks.Where(z => z != null).ToArray();
        int x;
        int y;
        int indx;
        for (int i = 0; i < currentFigureBlocks.Length; i++)
        {
            x = CurrentFigure.PositionX + currentFigureBlocks[i].LocalPositionX;
            y = CurrentFigure.PositionY + currentFigureBlocks[i].LocalPositionY;
            indx = y*BlocksXCount + x;
            Blocks[indx] = currentFigureBlocks[i];
            Blocks[indx].transform.parent = null;
            Blocks[indx].LocalPositionX = x;
            Blocks[indx].LocalPositionY = y;
            Blocks[indx].transform.SetParent(transform, true);
            Blocks[indx].Place(_levelSettings.GlobalEffectsSwitch);
        }

        CurrentFigure = CurrentFigure != null ? CurrentFigure.Destroy() : null;
        ShadowFigure = ShadowFigure != null ? ShadowFigure.Destroy() : null;
    }

    private void CheckLines()
    {
        var linesToRemove = new List<int>();
        for (int y = 0; y < BlocksYCount; y++)
        {
            var isRemoved = true;
            for (int x = 0; x < BlocksXCount; x++)
            {
                if (Blocks[y*BlocksXCount + x] == null)
                {
                    isRemoved = false;
                    break;
                }
            }
            if (isRemoved)
                linesToRemove.Add(y);
        }

        if (linesToRemove.Count > 0)
        {
            _removeLineSoundPlayer.Play();

            for (int i = linesToRemove.Count - 1; i >= 0; i--)
                RemoveLine(linesToRemove[i]);

            switch (linesToRemove.Count)
            {
                case 1:
                    _levelSettings.Score += _levelSettings.RemoveSingleLineScore * _levelSettings.LevelNumber;
                    break;
                case 2:
                    _levelSettings.Score += _levelSettings.RemoveTwoLineScore * _levelSettings.LevelNumber;
                    break;
                case 3:
                    _levelSettings.Score += _levelSettings.RemoveThreeLineScore * _levelSettings.LevelNumber;
                    break;
                case 4:
                    _levelSettings.Score += _levelSettings.RemoveFourLineScore * _levelSettings.LevelNumber;
                    break;
            }
        }
    }

    private void RemoveLine(int lineNumber)
    {
        if (lineNumber < 0 || lineNumber >= BlocksYCount) return;

        var y = lineNumber*BlocksXCount;
        for (int x = 0; x < BlocksXCount; x++)
        {
            if (Blocks[y + x] != null)
            {
                Blocks[y + x].Destroy(_levelSettings.GlobalEffectsSwitch);
                Blocks[y + x] = null;
            }
        }

        int indx;
        for (int yy = lineNumber + 1; yy < BlocksYCount; yy++)
        {
            for (int xx = 0; xx < BlocksXCount; xx++)
            {
                indx = yy*BlocksXCount + xx;
                if (Blocks[indx] != null)
                {
                    Blocks[indx].transform.parent = null;
                    Blocks[indx].LocalPositionY--;
                    Blocks[indx].transform.SetParent(transform, true);

                    Blocks[(yy - 1)*BlocksXCount + xx] = Blocks[indx];
                    Blocks[indx] = null;
                }
            }
        }

        _levelSettings.LinesRemoved++;
    }
}

Color.cs 

using UnityEngine;
using UnityEngine.UI;


public interface ISwitchButton
{
    void Switch();
}

[RequireComponent(typeof(Selectable)), DisallowMultipleComponent]
public abstract class ColoredSwitchButtonBase : MonoBehaviour, ISwitchButton
{
    // ===========================================================================================
    public Color SwitchedOnColor = Color.green;
    public Color SwitchedOffColor = Color.red;
    public bool SwitchedOn = true;
    private bool _lastSwitchedOn = true;

    private Selectable _selectable;


    // ===========================================================================================
    public virtual void Switch()
    {
        SwitchedOn = !SwitchedOn;
    }

    protected void Awake()
    {
        _selectable = GetComponent<Selectable>();
    }

    protected virtual void Update()
    {
        if (SwitchedOn != _lastSwitchedOn)
        {
            if (_selectable != null && _selectable.targetGraphic != null)
                _selectable.targetGraphic.color = SwitchedOn ? SwitchedOnColor : SwitchedOffColor;
        }
        _lastSwitchedOn = SwitchedOn;
    }
}

SlowMo.cs

using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityStandardAssets.ImageEffects;


[AddComponentMenu("Unique/SlowMo")]
public class SlowMo : UniqueComponentAtScene<SlowMo>
{
    // ===========================================================================================
    public float SlowMoBlocksEffect = 0.25F;
    public float SlowMoAudioEffect = 0.8F;
    [ReadOnly]
    public float SlowMoTime = 5F;
    public float SlowMoMaxTime = 5F;
    public float TimeToFullSlowMo = 0.09F;
    public float TimeToFullRestoreSlowMo = 60F;
    public Bloom BloomOnCamera;
    public float BloomThresholdMultiplier = 1.9F;

    private SpriteRenderer _renderer;

    private float _slowMoPressedTimer;
    private AudioSource[] _allAffectedAudioSources = new AudioSource[0];
    private float _defaultBloomThreshold;
    private LevelSettings _levelSettings;


    // ===========================================================================================
    public void Clear()
    {
        SlowMoTime = SlowMoMaxTime;
        _slowMoPressedTimer = 0F;
        Time.timeScale = 1F;

        if (_allAffectedAudioSources.Length > 0)
        {
            for (int i = 0; i < _allAffectedAudioSources.Length; i++)
                _allAffectedAudioSources[i].pitch = 1F;
        }
        _allAffectedAudioSources = new AudioSource[0];

        if (BloomOnCamera != null)
            BloomOnCamera.bloomThreshold = _defaultBloomThreshold;
    }

    protected override void Awake()
    {
        base.Awake();

        _renderer = GetComponentInChildren<SpriteRenderer>();

        if (BloomOnCamera == null)
        {
            var blooms = new List<Bloom>();
            for (int i = 0; i < Camera.allCamerasCount; i++)
            {
                var bloom = Camera.allCameras[i].GetComponent<Bloom>();
                if (bloom != null)
                    blooms.Add(bloom);
            }
            if (blooms.Count == 1)
                BloomOnCamera = blooms[0];
        }

        if (BloomOnCamera != null)
            _defaultBloomThreshold = BloomOnCamera.bloomThreshold;
    }

    protected void Start()
    {
        _levelSettings = LevelSettings.Instance;

        Clear();
    }

    protected void Update()
    {
        if (!Application.isPlaying || _levelSettings.IsPause) return;

        if (SlowMoTime < 0)
            _slowMoPressedTimer = 0F;
        else
        {
            if (hardInput.GetKeyDown("Slowmo"))
            {
                _slowMoPressedTimer = 0F;
                Time.timeScale = 1F;

                if (_allAffectedAudioSources.Length > 0)
                {
                    for (int i = 0; i < _allAffectedAudioSources.Length; i++)
                        _allAffectedAudioSources[i].pitch = 1F;
                }
                _allAffectedAudioSources = FindObjectsOfType<AudioSource>();

                if (BloomOnCamera != null)
                    BloomOnCamera.bloomThreshold = _defaultBloomThreshold;
            }
        }

        if (hardInput.GetKey("Slowmo") && SlowMoTime > 0)
        {
            SlowMoTime -= Time.deltaTime;
            _slowMoPressedTimer += Time.deltaTime;
        }

        if (!hardInput.GetKey("Slowmo"))
        {
            SlowMoTime += SlowMoMaxTime * (Time.deltaTime / TimeToFullRestoreSlowMo);
            SlowMoTime = Mathf.Clamp(SlowMoTime, 0F, SlowMoMaxTime);
            _slowMoPressedTimer -= Time.deltaTime;
        }

        _slowMoPressedTimer = Mathf.Clamp(_slowMoPressedTimer, 0F, TimeToFullSlowMo);
        UpdateSlowMo();

        if (_renderer != null)
        {
            var scale = SlowMoTime/SlowMoMaxTime;
            transform.localScale = new Vector3(scale, 1F, 1F);

            var red = scale > 0.5F ? 1F - 2F*(scale - 0.5F) : 1.0F;
            var green = scale > 0.5F ? 1.0F : 2F*scale;
            _renderer.color = new Color(red, green, 0F);
        }
    }


    // ===========================================================================================
    private void UpdateSlowMo()
    {
        var percent = _slowMoPressedTimer/TimeToFullSlowMo;

        Time.timeScale = 1F - (1F - SlowMoBlocksEffect)*percent;

        for (int i = 0; i < _allAffectedAudioSources.Length; i++)
            _allAffectedAudioSources[i].pitch = 1F - (1F - SlowMoAudioEffect) * percent;

        if (BloomOnCamera != null)
        {
            BloomOnCamera.bloomThreshold
                = _defaultBloomThreshold + (_defaultBloomThreshold/BloomThresholdMultiplier - _defaultBloomThreshold)*percent;
        }
    }
}