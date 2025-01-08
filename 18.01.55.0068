<?php
header("Content-Type: application/json; charset=UTF-8");
header("Access-Control-Allow-Origin: *");
header("Access-Control-Allow-Methods: GET, POST, PUT, DELETE");
header("Access-Control-Allow-Headers: Content-Type, Access-Control-Allow-Headers, Authorization, X-Requested-With");

require_once 'db.php'; // File untuk koneksi database

$request_method = $_SERVER['REQUEST_METHOD'];
$request_uri = explode('/', trim($_SERVER['PATH_INFO'], '/'));
$objek = $request_uri[0];
$id = isset($request_uri[1]) ? $request_uri[1] : null;

function send_response($status, $message, $data = null) {
    echo json_encode(["status" => $status, "message" => $message, "data" => $data]);
    exit();
}

function validate_input($input) {
    return htmlspecialchars(strip_tags($input));
}

switch ($request_method) {
    case 'GET':
        if ($id) {
            $stmt = $db->prepare("SELECT * FROM smartphones WHERE id = ?");
            $stmt->execute([$id]);
            $data = $stmt->fetch(PDO::FETCH_ASSOC);
            if ($data) {
                send_response("success", "Data found", $data);
            } else {
                send_response("error", "Data not found", null);
            }
        } else {
            $search = isset($_GET['search']) ? '%' . validate_input($_GET['search']) . '%' : '%';
            $stmt = $db->prepare("SELECT * FROM smartphones WHERE brand LIKE ? OR model LIKE ?");
            $stmt->execute([$search, $search]);
            $data = $stmt->fetchAll(PDO::FETCH_ASSOC);
            send_response("success", "Data retrieved", $data);
        }
        break;

    case 'POST':
        $input = json_decode(file_get_contents("php://input"), true);
        $brand = validate_input($input['brand'] ?? '');
        $model = validate_input($input['model'] ?? '');
        $price = validate_input($input['price'] ?? 0);
        $stock = validate_input($input['stock'] ?? 0);

        if (!$brand || !$model || !$price || !$stock) {
            send_response("error", "Invalid input", null);
        }

        $stmt = $db->prepare("INSERT INTO smartphones (brand, model, price, stock) VALUES (?, ?, ?, ?)");
        if ($stmt->execute([$brand, $model, $price, $stock])) {
            send_response("success", "Data created", ["id" => $db->lastInsertId()]);
        } else {
            send_response("error", "Failed to create data", null);
        }
        break;

    case 'PUT':
        if (!$id) {
            send_response("error", "ID is required", null);
        }
        $input = json_decode(file_get_contents("php://input"), true);
        $brand = validate_input($input['brand'] ?? '');
        $model = validate_input($input['model'] ?? '');
        $price = validate_input($input['price'] ?? 0);
        $stock = validate_input($input['stock'] ?? 0);

        if (!$brand || !$model || !$price || !$stock) {
            send_response("error", "Invalid input", null);
        }

        $stmt = $db->prepare("UPDATE smartphones SET brand = ?, model = ?, price = ?, stock = ? WHERE id = ?");
        if ($stmt->execute([$brand, $model, $price, $stock, $id])) {
            if ($stmt->rowCount() > 0) {
                send_response("success", "Data updated", null);
            } else {
                send_response("error", "Data not found", null);
            }
        } else {
            send_response("error", "Failed to update data", null);
        }
        break;

    case 'DELETE':
        if (!$id) {
            send_response("error", "ID is required", null);
        }
        $stmt = $db->prepare("DELETE FROM smartphones WHERE id = ?");
        if ($stmt->execute([$id])) {
            if ($stmt->rowCount() > 0) {
                send_response("success", "Data deleted", null);
            } else {
                send_response("error", "Data not found", null);
            }
        } else {
            send_response("error", "Failed to delete data", null);
        }
        break;

    default:
        send_response("error", "Method not allowed", null);
        break;
}
?>
