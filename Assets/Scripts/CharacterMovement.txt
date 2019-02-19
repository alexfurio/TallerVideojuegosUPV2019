using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class CharacterMovement : MonoBehaviour {

    [Header("Movement")]
    public float walkSpeed = 1f;

    [Header("Jumping")]
    public float jumpForce = 5f;
    public bool isGrounded = true;

    Transform child;
    public Rigidbody2D rb;

    //Raycasts
    private Vector3 rightOrigin;
    private Vector3 leftOrigin;
    public float width;
    public float heigth;
    LayerMask Ground;
    public float RayLength = 0.1f;

    // Use this for initialization
    void Start ()
    {
        child = transform.GetChild(0);
        rb = transform.GetComponent<Rigidbody2D>();
        Ground = 1 << LayerMask.NameToLayer("Ground");
    }


    // Update is called once per frame
    void Update ()
    {
        IsGrounded();
    }

    private void FixedUpdate()
    {
        Move();
        if (Input.GetButton("Jump")) Jump();
    }

    void Move()
    {
        float movement = Input.GetAxisRaw("Horizontal");
        if (movement > 0)
        {
            transform.Translate(new Vector3(walkSpeed * Time.deltaTime * movement, 0, 0));
            child.localScale = new Vector3(Mathf.Abs(child.localScale.x), child.localScale.y, 1);
        } else if (movement < 0)
        {
            transform.Translate(new Vector3(walkSpeed * Time.deltaTime * movement, 0, 0));
            child.localScale = new Vector3(-1 * Mathf.Abs(child.localScale.x), child.localScale.y, 1);
        }
    }

    void MoveForces()
    {
        float movement = Input.GetAxisRaw("Horizontal");
        if (movement > 0)
        {
            rb.velocity  = new Vector2(walkSpeed * movement, rb.velocity.y);
            child.localScale = new Vector3(Mathf.Abs(child.localScale.x), child.localScale.y, 1);
        }
        else if (movement < 0)
        {
            rb.velocity = new Vector2(walkSpeed * movement, rb.velocity.y);
            child.localScale = new Vector3(-1 * Mathf.Abs(child.localScale.x), child.localScale.y, 1);
        }
    }

    void Jump()
    {
        if (isGrounded)
        {
            rb.velocity = new Vector2(rb.velocity.x, 0);
            rb.AddForce(Vector2.up * jumpForce, ForceMode2D.Impulse);
            isGrounded = false;
        }
    }

    /*
     * Is the character grounded?
     * Uses raycasts at each side of the width of the character.
     */
    void IsGrounded()
    {
        rightOrigin = transform.position + new Vector3(width, -heigth / 2, 0);
        leftOrigin = transform.position + new Vector3(-width, -heigth / 2, 0);

        RaycastHit2D rightRay = Physics2D.Raycast(rightOrigin, -Vector3.up, RayLength, Ground);
        RaycastHit2D leftRay = Physics2D.Raycast(leftOrigin, -Vector3.up, RayLength, Ground);

        Debug.DrawLine(rightOrigin, rightOrigin + -Vector3.up * RayLength, Color.red);
        Debug.DrawLine(leftOrigin, leftOrigin + -Vector3.up * RayLength, Color.red);


        isGrounded = rightRay.collider != null || leftRay.collider != null;
    }

    private void OnTriggerEnter2D(Collider2D other)
    {
        if (other.gameObject.CompareTag("Enemy"))
        {
            Debug.Log("Auch!!");
        }
    }
}
