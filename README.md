# JassuMeena-
Rcing// GameCoordinates.java
import java.util.Random;

/**
 * Lightweight coordinate & geometry utilities for 2D/3D games.
 * Ready-to-use: Points, Vectors, Bounding boxes (AABB), utilities.
 */
public class GameCoordinates {

    // ---------- 2D Point / Vector ----------
    public static final class Point2 {
        public final double x;
        public final double y;
        public Point2(double x, double y) { this.x = x; this.y = y; }
        public Point2 add(Vec2 v) { return new Point2(x + v.x, y + v.y); }
        public Vec2 vectorTo(Point2 other) { return new Vec2(other.x - x, other.y - y); }
        @Override public String toString() { return String.format("Point2(%.3f, %.3f)", x, y); }
    }

    public static final class Vec2 {
        public final double x;
        public final double y;
        public Vec2(double x, double y) { this.x = x; this.y = y; }

        public Vec2 add(Vec2 v) { return new Vec2(x + v.x, y + v.y); }
        public Vec2 sub(Vec2 v) { return new Vec2(x - v.x, y - v.y); }
        public Vec2 mul(double s) { return new Vec2(x * s, y * s); }
        public double dot(Vec2 v) { return x * v.x + y * v.y; }
        public double length() { return Math.sqrt(x * x + y * y); }
        public Vec2 normalize() {
            double len = length();
            return len == 0 ? new Vec2(0,0) : new Vec2(x / len, y / len);
        }
        @Override public String toString() { return String.format("Vec2(%.3f, %.3f)", x, y); }
    }

    // ---------- 3D Point / Vector ----------
    public static final class Point3 {
        public final double x, y, z;
        public Point3(double x, double y, double z) { this.x = x; this.y = y; this.z = z; }
        public Point3 add(Vec3 v) { return new Point3(x + v.x, y + v.y, z + v.z); }
        public Vec3 vectorTo(Point3 other) { return new Vec3(other.x - x, other.y - y, other.z - z); }
        @Override public String toString() { return String.format("Point3(%.3f, %.3f, %.3f)", x, y, z); }
    }

    public static final class Vec3 {
        public final double x, y, z;
        public Vec3(double x, double y, double z) { this.x = x; this.y = y; this.z = z; }
        public Vec3 add(Vec3 v) { return new Vec3(x + v.x, y + v.y, z + v.z); }
        public Vec3 mul(double s) { return new Vec3(x * s, y * s, z * s); }
        public double dot(Vec3 v) { return x*v.x + y*v.y + z*v.z; }
        public double length() { return Math.sqrt(x*x + y*y + z*z); }
        public Vec3 normalize() {
            double len = length();
            return len == 0 ? new Vec3(0,0,0) : new Vec3(x/len, y/len, z/len);
        }
        @Override public String toString() { return String.format("Vec3(%.3f, %.3f, %.3f)", x, y, z); }
    }

    // ---------- Axis-Aligned Bounding Boxes (AABB) ----------
    public static final class AABB2 {
        public final double minX, minY, maxX, maxY;
        public AABB2(double minX, double minY, double maxX, double maxY) {
            this.minX = Math.min(minX, maxX);
            this.minY = Math.min(minY, maxY);
            this.maxX = Math.max(minX, maxX);
            this.maxY = Math.max(minY, maxY);
        }
        public boolean contains(Point2 p) {
            return p.x >= minX && p.x <= maxX && p.y >= minY && p.y <= maxY;
        }
        public boolean intersects(AABB2 other) {
            return !(other.maxX < minX || other.minX > maxX || other.maxY < minY || other.minY > maxY);
        }
        @Override public String toString() {
            return String.format("AABB2[(%.3f,%.3f)-(%.3f,%.3f)]", minX, minY, maxX, maxY);
        }
    }

    public static final class AABB3 {
        public final double minX, minY, minZ, maxX, maxY, maxZ;
        public AABB3(double minX, double minY, double minZ, double maxX, double maxY, double maxZ) {
            this.minX = Math.min(minX, maxX);
            this.minY = Math.min(minY, maxY);
            this.minZ = Math.min(minZ, maxZ);
            this.maxX = Math.max(minX, maxX);
            this.maxY = Math.max(minY, maxY);
            this.maxZ = Math.max(minZ, maxZ);
        }
        public boolean contains(Point3 p) {
            return p.x >= minX && p.x <= maxX &&
                   p.y >= minY && p.y <= maxY &&
                   p.z >= minZ && p.z <= maxZ;
        }
        public boolean intersects(AABB3 other) {
            return !(other.maxX < minX || other.minX > maxX ||
                     other.maxY < minY || other.minY > maxY ||
                     other.maxZ < minZ || other.minZ > maxZ);
        }
        @Override public String toString() {
            return String.format("AABB3[(%.3f,%.3f,%.3f)-(%.3f,%.3f,%.3f)]",
                    minX,minY,minZ,maxX,maxY,maxZ);
        }
    }

    // ---------- Utility methods ----------
    public static final class CoordinateUtils {
        private static final Random RNG = new Random();

        // Distance between points
        public static double distance(Point2 a, Point2 b) {
            double dx = a.x - b.x;
            double dy = a.y - b.y;
            return Math.sqrt(dx*dx + dy*dy);
        }
        public static double distance(Point3 a, Point3 b) {
            double dx = a.x - b.x;
            double dy = a.y - b.y;
            double dz = a.z - b.z;
            return Math.sqrt(dx*dx + dy*dy + dz*dz);
        }

        // Clamp value between min and max
        public static double clamp(double value, double min, double max) {
            if (value < min) return min;
            if (value > max) return max;
            return value;
        }

        // Random point in rectangle (2D)
        public static Point2 randomPoint2(double minX, double minY, double maxX, double maxY) {
            double x = minX + RNG.nextDouble() * (maxX - minX);
            double y = minY + RNG.nextDouble() * (maxY - minY);
            return new Point2(x, y);
        }

        // Snap to grid (2D)
        public static Point2 snapToGrid(Point2 p, double gridSize) {
            double gx = Math.round(p.x / gridSize) * gridSize;
            double gy = Math.round(p.y / gridSize) * gridSize;
            return new Point2(gx, gy);
        }

        // Linear interpolation between two points (2D)
        public static Point2 lerp(Point2 a, Point2 b, double t) {
            t = clamp(t, 0.0, 1.0);
            return new Point2(a.x + (b.x - a.x) * t, a.y + (b.y - a.y) * t);
        }

        // Move point towards target with max step
        public static Point2 moveTowards(Point2 from, Point2 to, double maxStep) {
            Vec2 dir = from.vectorTo(to);
            double dist = dir.length();
            if (dist <= maxStep || dist == 0) return to;
            Vec2 step = dir.normalize().mul(maxStep);
            return from.add(step);
        }
    }

    // ---------- Demo main to show usage ----------
    public static void main(String[] args) {
        System.out.println("=== Game Coordinates Demo ===");

        // 2D examples
        Point2 p1 = new Point2(1.5, 2.0);
        Point2 p2 = new Point2(4.0, 6.0);
        System.out.println("p1 = " + p1);
        System.out.println("p2 = " + p2);
        System.out.printf("Distance p1->p2 = %.3f%n", CoordinateUtils.distance(p1, p2));

        Vec2 dir = p1.vectorTo(p2);
        System.out.println("Direction vector = " + dir + " length=" + dir.length());

        Point2 moved = CoordinateUtils.moveTowards(p1, p2, 2.0);
        System.out.println("Move p1 towards p2 by 2.0 -> " + moved);

        // Snap to grid
        Point2 snapped = CoordinateUtils.snapToGrid(new Point2(3.14, 2.71), 1.0);
        System.out.println("Snapped to grid (1.0): " + snapped);

        // AABB collision
        AABB2 boxA = new AABB2(0, 0, 3, 3);
        AABB2 boxB = new AABB2(2, 2, 4, 4);
        System.out.println("boxA intersects boxB? " + boxA.intersects(boxB));

        // Random spawn
        Point2 spawn = CoordinateUtils.randomPoint2(-10, -5, 10, 5);
        System.out.println("Random spawn point = " + spawn);

        // 3D example
        Point3 q1 = new Point3(0,0,0);
        Point3 q2 = new Point3(1,2,3);
        System.out.printf("3D distance = %.3f%n", CoordinateUtils.distance(q1, q2));
    }
}
